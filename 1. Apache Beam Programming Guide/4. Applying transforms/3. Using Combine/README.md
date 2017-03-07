### Using Combine

`Combine`은 데이터에서 element 또는 value 의 collection 을 결합하는 Beam transform 입니다. `Combine`에는 전체 `PCollection` 에서 작동하는 transform과 key/value 쌍의 PCollection에서 각 key의 값을 결합하는 transform이 있습니다.

당신이 `Combine` transform 을 적용하면, 당신은 element 나 value 를 결합하기 위한 로직을 함수에 추가해야 합니다. combining function 은 교환 가능하고 결합적이어야합니다. 함수가 주어진 key가있는 모든 value에서 정확히 한 번 호출되는 것은 아니기 때문입니다. 다수의 worker 에서 입력 데이터가 분산되고, combining function 은 부분적인 결합을 위해 여러번 호출 될 것입니다. Beam SDK 는 역시 몇몇 미리 만들어진 sum, min, max 등과 같이 많이 쓰이는 combine function 을 제공합니다.

간단한 결합 오퍼레이션(예를들어 합) 은 간단한 함수로 구현될 수 있습니다. 더 복잡한 결합 오퍼레이션은 입/출력 타입에 대한 축적된 타입이 있는 `CombineFn`의 서브클래스를 만드는 작업이 필요합니다.

#### Simple combinations using simple functions

다음의 예제 코드는 간단한 combine function 을 보여줍니다.

```java
// Sum a collection of Integer values. The function SumInts implements the interface SerializableFunction.
public static class SumInts implements SerializableFunction<Iterable<Integer>, Integer> {
  @Override
  public Integer apply(Iterable<Integer> input) {
    int sum = 0;
    for (int item : input) {
      sum += item;
    }
    return sum;
  }
}
```

#### Advanced combinations using CombineFn

더 복잡한 combine function 을 위해, 당신은 `CombineFn`의 서브클래스를 정의해야합니다. 더 세련된 accumulator 이 combine function 에서 요구된다면 당신은 `CombineFn`을 사용해야 하며, 추가 사전 처리 또는 사후 처리를 수행하고 출력 유형을 변경하거나 key를 고려해야합니다.

일반적인 combining 오퍼레이션은 네개의 오퍼레이션으로 구성됩니다. 당신이 `CobineFn`의 서브클래스를 만들 때, 당신은 네개의 오퍼레이션을 올바른 함수를 오버라이딩 해야합니다.

1. **Create Accumulator** 새로운 "local" accumulator를 만듭니다. 예제의 경우, 평균을 취하면, 로컬 누산기는 누적 값 (최종 평균 분할의 분자 값)과 지금까지 합산 된 값의 수 (분모 값)를 추적합니다. 이는 분산 된 방식으로 여러 번 호출 될 수 있습니다.
2. **Add Input** value 를 리턴하면서 accumulator에서 입력 element 를 더합니다. 예제에서, 이것은 합을 업데이트하고 카운트를 증가시킬 것입니다. 역시 parallel 하게 동작합니다.
3. **Merge Accumulators** 여러개의 accumulator 를 하나의 accumulator 로 합칩니다. 이것은 여러개의 accumulator 에 있는 데이터가 어떻게 마지막 계산 전 합쳐지는지를 나타냅니다. 평균 계산에서, 분할의 각 부분을 나타내는 accumulator 들이 함께 병합됩니다. 여러 번 출력에 대해 다시 호출 될 수 있습니다.
4. **Extract Output** 마지막 계산을 수행합니다. 평균을 계산할 경우, 이는 모든 값의 결합 된 합계를 합한 값의 수로 나누는 것을 의미합니다. 마지막으로 합친 accumulator에서 한 번 호출됩니다.

다음의 예제 코드는 평균을 구하기 위해 어떻게 `CombineFn`을 정의하는지 보여줍니다.

```java
public class AverageFn extends CombineFn<Integer, AverageFn.Accum, Double> {
  public static class Accum {
    int sum = 0;
    int count = 0;
  }

  @Override
  public Accum createAccumulator() { return new Accum(); }

  @Override
  public Accum addInput(Accum accum, Integer input) {
      accum.sum += input;
      accum.count++;
      return accum;
  }

  @Override
  public Accum mergeAccumulators(Iterable<Accum> accums) {
    Accum merged = createAccumulator();
    for (Accum accum : accums) {
      merged.sum += accum.sum;
      merged.count += accum.count;
    }
    return merged;
  }

  @Override
  public Double extractOutput(Accum accum) {
    return ((double) accum.sum) / accum.count;
  }
}
```
만약 당신이 key-value 쌍의 `PCollection` 을 합치고 있다면, [per-key combining](https://beam.apache.org/documentation/programming-guide/#transforms-combine-per-key)로 충분할 것입니다. 키에 따라 변경하기위한 결합 전략이 필요한 경우 (예 : 일부 사용자의 경우 MIN, 다른 사용자의 경우 MAX), 결합 전략에서 `KeyedCombineFn`을 정의하여 키에 액세스 할 수 있습니다.

#### Combining a PCollection into a single value

global combine을 사용하여 주어진 PCollection의 모든 element를 pipeline에서 하나의 element를 포함하는 새로운 PCollection으로 표현되는 단일 값으로 변환합니다. 다음 예제 코드는 Beam의 sum combine 함수를 적용하여 Integer의 PCollection에 대한 합계 값을 생성하는 방법을 보여줍니다.

```java
// Sum.SumIntegerFn() combines the elements in the input PCollection.
// The resulting PCollection, called sum, contains one value: the sum of all the elements in the input PCollection.
PCollection<Integer> pc = ...;
PCollection<Integer> sum = pc.apply(
   Combine.globally(new Sum.SumIntegerFn()));
```

#### Global windowing

입력 된 `PCollection`이 기본 전역 윈도우(Global windowing) 처리를 사용하는 경우 기본 동작은 하나의 항목을 포함하는 `PCollection`을 반환하는 것입니다. 해당 항목의 value는 `Combine`을 적용 할 때 지정한 combine function 의 accumulator에서 가져옵니다.예를 들어, Beam 에서 제공하는 sum combine function 은 0 값 (빈 입력의 합계)을 반환하지만 min combine function 은 최대 또는 무한 값을 반환합니다.(???)

입력이 비어있는 경우 `Combine`이 빈 PCollection을 반환하게하려면 다음 코드와 같이 `Combine` 변환을 적용 할 때 `.withoutDefaults`를 지정하십시오.
예제 :

```java
PCollection<Integer> pc = ...;
PCollection<Integer> sum = pc.apply(
  Combine.globally(new Sum.SumIntegerFn()).withoutDefaults());
```

#### Non-global windowing

만약 당신의 `PCollection`이 non-global windowing function 을 사용한다면, Beam 은 default behavior 를 제공하지 않습니다. 당신은 `Combine`을 적용할 때, 다음의 옵션 중 하나를 명시해야합니다.
- `.withoutDefaults`를 명시합니다. : 빈 입력 `PCollection` 이라면 빈 출력 `PCollection` 이 나옵니다.
- `.asSingletonView`를 명시합니다. 출력이 `PCollectionView`로 즉시 변환됩니다. 이는 각각의 빈 윈도우에 대해 default 값을 제공합니다. 당신은 만약 pipeline의 `Combine`이 추후에 pipeline의 입력으로 사용될 경우에만 이 옵션을 사용하면 됩니다.

#### Combining values in a key-grouped collection

`GroupByKey` transform 을 사용하거나 해서 key-grouped collection 을 만든 후에, 일반적인 패턴은 각 키의 collection 들을 하나의 value 로 합치는 것입니다. 이전 예제에서 `GroupByKey` transform 을 사용해서 key-grouped PCollection (`groupedWords`)를 만든 것은 다음과 같았습니다.

```
cat, [1,5,9]
dog, [5,2]
and, [1,2,6]
jump, [3]
tree, [2]
...
```

위의 `PCollection`에서, 각각의 element 는 string key 를 가집니다. (ex, "cat") 그리고 value 로는 반복 가능한 integer 들을 가집니다 (ex, [1,5,9]). 만약 당신의 pipeline 의, 다음 동작이 value 들을 combine 하는것이라면, 당신은 integer 묶음들을 하나의, 합쳐진 값으로 combine 할 수 있습니다. value 들을 묶음으로서 얻게되는 이 `GroupByKey`패턴은, Beam의 Combine PerKey transform 과 같습니다. PerKey Combine에 제공하는 combine function은 associative reduction function 이거나 `CombineFn`의 서브 클래스 여야합니다.

```java
// PCollection is grouped by key and the Double values associated with each key are combined into a Double.
PCollection<KV<String, Double>> salesRecords = ...;
PCollection<KV<String, Double>> totalSalesPerPerson =
  salesRecords.apply(Combine.<String, Double, Double>perKey(
    new Sum.SumDoubleFn()));

// The combined value is of a different type than the original collection of values per key.
// PCollection has keys of type String and values of type Integer, and the combined value is a Double.

PCollection<KV<String, Integer>> playerAccuracy = ...;
PCollection<KV<String, Double>> avgAccuracyPerPlayer =
  playerAccuracy.apply(Combine.<String, Integer, Double>perKey(
    new MeanInts())));
```
