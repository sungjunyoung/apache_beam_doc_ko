### Using ParDo

`ParDo`는 generic parallel processing 을 위한 Beam transform 입니다. `ParDo` 처리 패러다임은 Map / Shuffle / Reduce 스타일 알고리즘의 "Map"단계와 유사합니다: `ParDo` transform 은 입력 PCollection 내의 각각의 element에 대해서 함수(user code)를 수행합니다. 그리고 0개 1개 혹은 다수의  PCollection 내 element 를 출력합니다.

`ParDo`는 데이터 프로세싱 명령의 다방면에서 매우 유용합니다.
- **Filtering a data set.** 당신은 `PCollection` 내의 각각의 element 들에 대해서 필터링하여 결과를 내기거나 무시하기 위해 `ParDo`를 사용할 수 있습니다.
- **Formatting or type-converting each element in a data set.** 만약 당신의 입력 `PCollection`의 element들이 당신이 원하지 않는 다른 타입이나 포맷을 가지고 있다면, 당신은 `ParDo`를 사용해 원하는 포맷 / 타입으로 변경해 새로운 PCollection 을 받아올 수 있습니다.
- **Extracting parts of each element in a data set.** 만약 당신이 다수의 필드를 가진 레코드의 `PCollection`을 가지고 있다면, 당신은 `ParDo`를 당신이 원하는 필드만 추려내도록 사용할 수 있습니다.
- **Performing computations on each element in a data set** 당신은 간단하거나 복잡한 계산을 `PCollection`의 모든 element나 특정 element에 `ParDo`를 사용해 적용시켜 새로운 결과의 PCollection 을 반환받을수 있습니다.

이런 역할로, `ParDo`는 pipeline 에서 공통된 중간 스탭입니다. 당신은 raw 입력 레코드의 집합에서 특정한 필드만 뽑아내거나, 다른 포맷으로 변경하는 등의 작업으로 사용할 수 있습니다. 또한, 당신은 `ParDo`를 사용하여 처리 된 데이터를 데이터베이스 테이블 행이나 인쇄 가능한 문자열과 같은 출력에 적합한 형식으로 변환 할 수도 있습니다.

당신이 `ParDo` transform 을 수행할 때, 당신은 `DoFn` 오브젝트의 형태로 user code 를 제공해야합니다. `DoFn`은 Beam SDK에 정의된 분산 처리 함수가 정의된 클래스입니다.

> **Note** DoFn 의 서브클래스를 만들때, 서브클래스는 [General Requirements for Writing User Code for Beam Transforms](https://beam.apache.org/documentation/programming-guide/#transforms-usercodereqs)를 따라야 함에 유의하십시오

#### Applying ParDo

모든 Beam transform가 그렇듯, 다음 예제 코드와 같이 입력 `PCollection`에서 `apply` 메소드를 호출하고 `ParDo`를 인수로 전달하여 `ParDo`를 적용합니다.

```java
// The input PCollection of Strings.
PCollection<String> words = ...;

// The DoFn to perform on each element in the input PCollection.
static class ComputeWordLengthFn extends DoFn<String, Integer> { ... }

// Apply a ParDo to the PCollection "words" to compute lengths for each word.
PCollection<Integer> wordLengths = words.apply(
    ParDo
    .of(new ComputeWordLengthFn()));        // The DoFn to perform on each element, which
                                            // we define above.
```
예제에서, 입력은 `String` 값을 포함하는 `PCollection`입니다. 우리는 각각의 string 의 길이를 계산하기 위해 특정한 함수(`ComputeWordLengthFn`)인 `ParDo` transform 을 적용했습니다. 그리고 결과는 각각의 단어의 길이를 저장하고 있는 `Integer` 타입인 새로운 `PCollection`이 됩니다.

#### Creating a DoFn

당신이 `ParDo`로 넘긴 `DoFn` 오브젝트는 입력 collection의 elements 에서 수행되는 로직을 포함합니다. 당신이 Beam 을 사용할 때, 당신이 작성해야 할 가장 중요한 코드는 바로 당신의 pipeline의 데이터 프로세싱 task 를 정의하는 `DoFn`입니다.

> **Note** `DoFn`을 만들 때, [General Requirements for Writing User Code for Beam Transforms](https://beam.apache.org/documentation/programming-guide/#transforms-usercodereqs)에 신경쓰고, 이것을 따랏는지 확인하십시오.

이 `DoFn`은 입력 `PCollection`으로부터 한번에 하나의 요소를 처리합니다. 당신이 `DoFn`의 서브클래스를 만들때, 당신은 입력과 출력 요소에 맞는 타입을 파라미터로 제공해야 합니다. 만약 당신이 `DoFn`이 입력으로 `String` 타입을 받고, 출력으로 `Integer` 타입의 출력 collection 내 element 를 준다면, 당신의 클래스(`ComputeWordLengthFn`)는 다음과 같이 정의되어야 할 것입니다.

```java
static class ComputeWordLengthFn extends DoFn<String, Integer> { ... }
```

`DoFn` 서브클래스 안에서, 당신은 실제 수행하는 로직을 제공하는 함수 (`@ProcessElement` annotation 이 달린)를 작성할 것입니다. 당신은 수동으로 입력 collection 의 element 들을 추출할 필요가 없습니다. Beam SDK 가 그것을 당신을 위해 수행해 줄 것입니다. `ProcessContext` 오브젝트는 당신에게 입력 element 와 element 를 출력하기위한 함수의 접근을 줄 것입니다.

```java
static class ComputeWordLengthFn extends DoFn<String, Integer> {
  @ProcessElement
  public void processElement(ProcessContext c) {
    // Get the input element from ProcessContext.
    String word = c.element();
    // Use ProcessContext.output to emit the output element.
    c.output(word.length());
  }
}
```

> **Note** 만약 당신의 입력 PCollection 의 element 가 key/value 쌍으로 되어 있다면, 당신은 key 나 value 에 대해서 다음과 같이 씀으로서 접근할 수 있습니다. `ProcessContext.element().getKey()` / `ProcessContext.element().getValue()`

주어진 DoFn 인스턴스는 일반적으로 element의 임의 번들을 처리하기 위해 한 번 이상 호출됩니다. 그러나, Beam 은 정확한 호출 횟수를 보장하지 않습니다. worker 노드의 실패나 재시도 횟수에 따라 여러번 호출될 수도 있습니다. 이렇게, 처리 메소드에 대한 여러 호출에서 정보를 캐싱 할 수 있지만 그렇게 할 경우 구현이 호출 수에 의존하지 않는지 확인하십시오.

처리 중 Beam과 처리 백 엔드가 안전하게 pipeline 내의 값을 직렬화하고 캐시 할 수 있도록하려면 일부 불변의 요구 사항을 충족해야합니다. 방법이 다음 요구 사항을 충족해야합니다.

- `ProcessContext.element()`나 `ProcessContext.sideInput()`을 리턴함으로서 element 를 변형하지 말아야 합니다.
- 일단 `ProcessContext.output()`이나 `ProcessContext.sideOutput()`으로 값을 출력한 경우에는 수정해서는 안됩니다.

#### Lightweight DoFns and other abstractions

만약 당신의 함수가 비교적 직관적이라면, 당신은 경량화된 익명 내부 클래스 인스턴스로 in-line `DoFn`을 제공함으로써 `ParDo`의 사용을 단순화 시킬수 있습니다.

여기에 이전 예제가 있습니다. 익명 내부 클래스 인스턴스로 지정된 DoFn과 함께 ComputeLengthWordsFn이있는 ParDo 입니다.

```java
// The input PCollection.
PCollection<String> words = ...;

// Apply a ParDo with an anonymous DoFn to the PCollection words.
// Save the result as the PCollection wordLengths.
PCollection<Integer> wordLengths = words.apply(
  "ComputeWordLengths",                     // the transform name
  ParDo.of(new DoFn<String, Integer>() {    // a DoFn as an anonymous inner class instance
      @ProcessElement
      public void processElement(ProcessContext c) {
        c.output(c.element().length());
      }
    }));
```

만약 `ParDo`가 입력 element 와 출력 element 에 대해 1:1 로 수행한다면, 각각의 입력 element 에 대해서, 정확히 하나의 출력 요소를 생성하는 함수를 적용하면 더 높은 수준의 MapElements 변환을 사용할 수 있습니다. MapElements는 추가 간결성을 위해 익명의 Java 8 람다 함수를 허용 할 수 있습니다.

```java
// The input PCollection.
PCollection<String> words = ...;

// Apply a MapElements with an anonymous lambda function to the PCollection words.
// Save the result as the PCollection wordLengths.
PCollection<Integer> wordLengths = words.apply(
  MapElements.via((String word) -> word.length())
      .withOutputType(new TypeDescriptor<Integer>() {});
```

> **Note** Java 8 lambda function을 여러 다른 `Filter`,`FlatMapElements` 등 Beam Transform 에서 사용할 수 있습니다.
