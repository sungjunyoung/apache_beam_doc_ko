### Testing Individual DoFn Objects

파이프라인의 `DoFn` function 은 자주 실행되며, 자주 여러 개의 Compute Engine instance 에서 실행됩니다. 당신의 `DoFn` 오브젝트를 runner service 를 이용해 실행하기전 테스트 하는 것은 디버깅 시간과 에너지를 절약하는 데 좋은 방법입니다.

Beam Java SDK 는 개개의 `DoFn` 을 테스트 하는데에 있어 [DoFnTester](https://github.com/apache/beam/blob/master/sdks/java/core/src/test/java/org/apache/beam/sdk/transforms/DoFnTesterTest.java)라는 편리한 방법을 제공합니다. 이것은 `Transform`패키지 내에 존재합니다.

`DoFnTester`는 [JUnit](http://junit.org/junit4/) 프레임워크를 사용합니다. `DoFnTester`를 사용하기 위해서, 당신은 다음을 따라야 합니다.

1. `DoFnTester`를 만듭니다. 당신은 당신이 테스트하고자 하는 `DoFn` 의 인스턴스를 넘겨주어야 합니다.
2. 당신의 `DoFn`의 적절한 타입의 하나 혹은 그 이상의 테스트 입력을 만듭니다. 만약 당신의 `DoFn`이 side input 혹은 output 을 사용한다면, side input/output 태그를 만들어야 합니다.
3. 메인 입력의 수행을 위해 `DoFnTester.processBunle`를 호출합니다.
4. 당신이 예측하는 결과와 `processBundle`에서 나온 테스트 결과가 일치하는지 확인하기 위해 JUnit의 `Assert.asserThat`을 사용합니다.

#### Creating a DoFnTester

`DoFnTester`를 만들기 위해, 처음으로 당신이 테스트하기 원하는 `DoFn` 인스턴스를 만들어야 합니다. 그러고 나서, 당신이 `DoFnTester`를 만들 때, `.of()` static factory 메서드를 사용해 인스턴스를 사용합니다.

```java
static class MyDoFn extends DoFn<String, Integer> { ... }
  MyDoFn myDoFn = ...;

  DoFnTester<String, Integer> fnTester = DoFnTester.of(myDoFn);
```

##### Creating Test Inputs

당신의 `DoFn`으로 보내기 위해 `DoFnTester`를 위한 한개 이상의 입력을 만들어 줄 필요가 있습니다. 테스트 입력들을 만들기 위해서, 간단히 `DoFn`이 받는 입력과 동일한 타입의 입력의 하나 이상의 입력 변수를 만들어 줍니다.

```java
static class MyDoFn extends DoFn<String, Integer> { ... }
MyDoFn myDoFn = ...;
DoFnTester<String, Integer> fnTester = DoFnTester.of(myDoFn);

String testInput = "test1";
```

###### Side Inputs and Outputs

당신의 `DoFn`이 side inputs 를 받는다면, 당신은 `DoFnTester.setSideInputs` 메소드를 사용해서 side inputs 를 만들 수 있습니다.

```java

static class MyDoFn extends DoFn<String, Integer> { ... }
MyDoFn myDoFn = ...;
DoFnTester<String, Integer> fnTester = DoFnTester.of(myDoFn);

PCollectionView<List<Integer>> sideInput = ...;
Iterable<Integer> value = ...;
fnTester.setSideInputInGlobalWindow(sideInput, value);
```

만약 당신의 `DoFn`이 side output 을 만든다면, 당신은 적절한 각각의 출력에 접근하는데 사용되는 `TupleTag` 오브젝트를 설정할 필요가 있습니다. side output 을 만드는 `DoFn` 은 각각의 side output 으로 `PCollectionTuple`을 만듭니다. 당신은 tuple 안의 각각의 side output 에 일치하는 `TupleTagList`를 제공해야합니다.

당신의 `DoFn`이 side output 으로 `String`과 `Integer` 를 출력한다고 가정하십시오. 당신은 각각에 대해 `TupleTag` 오브젝트를 만들고, 그들을 `TupleTagList`로 만듭니다. 그리고 `DoFnTester`를 다음과 같이 설정합니다.

```java
static class MyDoFn extends DoFn<String, Integer> { ... }
MyDoFn myDoFn = ...;
DoFnTester<String, Integer> fnTester = DoFnTester.of(myDoFn);

TupleTag<String> tag1 = ...;
TupleTag<Integer> tag2 = ...;
TupleTagList tags = TupleTagList.of(tag1).and(tag2);

fnTester.setSideOutputTags(tags);
```

더 많은 정보는 [side inputs](https://beam.apache.org/documentation/programming-guide/#transforms-sideio)의 `ParDo` 도큐먼트를 보십시오.

#### Processing Test Inputs and Checking Result

입력을 처리하기 위해서(그리고 당신의 `DoFn`의 테스트를 실행하기 위해서), 당신은 `DoFnTester.processBunble` 메서드를 호출합니다. `processBunle`을 호출하면, 당신의 `DoFn`을 위한 하나 이상의 메인 테스트 입력을 넘겨줍니다. 당신이 side input 을 설정한다면, side input 은 당신이 제공하는 메인 입력의 각각의 batch 에서 이용가능합니다.

`DoFnTester.processBunle`은 `DoFn`에서 지정한 특정 출력 타입과 같은 오브젝트인 출력의 `List`를 리턴합니다. 예를들어, `DoFn<String, Integer>`에서는, `processBunle`은 `List<Integer>`를 리턴합니다.

```java
static class MyDoFn extends DoFn<String, Integer> { ... }
MyDoFn myDoFn = ...;
DoFnTester<String, Integer> fnTester = DoFnTester.of(myDoFn);

String testInput = "test1";
List<Integer> testOutputs = fnTester.processBundle(testInput);
```

`processBundle`의 결과를 체크하고, 출력의 `List`가 당신이 예상한 값을 포함ㅎ나느지 알기 위해서, 당신은 JUnit의 `Assert.assertThat`메서드를 사용합니다.

```java
String testInput = "test1";
List<Integer> testOutputs = fnTester.processBundle(testInput);

Assert.assertThat(testOutputs, Matchers.hasItems(...));

// Process a larger batch in a single step.
Assert.assertThat(fnTester.processBundle("input1", "input2", "input3"), Matchers.hasItems(...));
```
