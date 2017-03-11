### Testing Composite Transform

당신이 만든 composite transform 을 테스트하기 위해서는, 당신은 다음과 같은 패턴을 사용할 수 있습니다.

- `TestPipeline`을 만듭니다.
- 몇몇 고정적인 테스트 입력 데이터를 만듭니다.
- 당신의 입력 데이터의 `PCollection`을 만들기 위해 `Create` transform 을 사용합니다.
- 입력 `PCollection`에 당신의 composite transform 을 `apply` 시키고, 출력 `PCollection` 결과를 저장합니다.
- `PAssert`와 출력 `PCollection`이 당신이 예상하는 element 를 포함하는지 확인하기 위해 그것의 서브클래스를 사용합니다.

#### TestPipeline

[TestPipeline](https://github.com/apache/beam/blob/master/sdks/java/core/src/main/java/org/apache/beam/sdk/testing/TestPipeline.java)은 테스트 transform 을 위한 Beam Java SDK 안에 포함된 클래스입니다. 테스트의 경우 파이프 라인 개체를 만들 때 `Pipeline` 대신 `TestPipeline`을 사용하십시오.

다음과 같이 `TestPipeline`을 만듭니다.

```java
Pipeline p = TestPipeline.create();
```

> **Note** unbounded 파이프라인에 대한 테스트는 [이 포스트](https://beam.apache.org/blog/2016/10/20/test-stream.html)를 참고하십시오

#### Using the Create Transform

`Create` 변환을 사용하여 Java List와 같은 표준인 메모리 컬렉션 클래스에서 `PCollection`을 만들 수 있습니다. 자세한 내용은 [Creating a PCollection](https://beam.apache.org/documentation/programming-guide/#pcollection)를 참조하십시오.

#### PAssert

[PAssert](https://beam.apache.org/documentation/sdks/javadoc/0.5.0/index.html?org/apache/beam/sdk/testing/PAssert.html) 는 Beam Java SDK에 포함된 클래스입니다. 당신은 `PCollection`이 특정한 예상된 element 를 포함하고 있는지 확인하기 위해 `PAssert`를 사용할 수 있습니다.

주어진 `PCollection`에서, 당신은 아래와 같이 내용을 확인하기 위해 `PAssert`를 사용할 수 있습니다.

```java
PCollection<String> output = ...;

// Check whether a PCollection contains some elements in any order.
PAssert.that(output)
.containsInAnyOrder(
  "elem1",
  "elem3",
  "elem2");
```

`PAssert`를 사용하는 어떤 코드든 `JUnit`과 `Hamcrest`에 링크되어야 합니다. 만약 당신이 Maven을 사용한다면, 당신은 다음의 의존성을 프로젝트의 pom.xml 파일에 추가함으로서 `Hamcrest`에 링크할 수 있습니다.

```xml
<dependency>
    <groupId>org.hamcrest</groupId>
    <artifactId>hamcrest-all</artifactId>
    <version>1.3</version>
    <scope>test</scope>
</dependency>
```

이 클래스가 어떻게 작동하는지에 대한 더 많은 정보를 보려면, [org.apache.beam.sdk.testing](https://beam.apache.org/documentation/sdks/javadoc/0.5.0/index.html?org/apache/beam/sdk/testing/package-summary.html) 패키지 도큐멘테이션을 참고하십시오.

#### An Example Test for a Composite Transform

다음은 전체 composite transform 의 테스트 코드를 보여줍니다. 테스트는 `String` element 의 입력 `PCollection`에 `Count` transform 테스트를 적용합니다. 테스트는 Java `List<String>`으로부터 입력 `PCollection`을 만들기 위해 `Create` transform 을 사용합니다.

```java
public class CountTest {

// Our static input data, which will make up the initial PCollection.
static final String[] WORDS_ARRAY = new String[] {
"hi", "there", "hi", "hi", "sue", "bob",
"hi", "sue", "", "", "ZOW", "bob", ""};

static final List<String> WORDS = Arrays.asList(WORDS_ARRAY);

public void testCount() {
  // Create a test pipeline.
  Pipeline p = TestPipeline.create();

  // Create an input PCollection.
  PCollection<String> input = p.apply(Create.of(WORDS)).setCoder(StringUtf8Coder.of());

  // Apply the Count transform under test.
  PCollection<KV<String, Long>> output =
    input.apply(Count.<String>perElement());

  // Assert on the results.
  PAssert.that(output)
    .containsInAnyOrder(
        KV.of("hi", 4L),
        KV.of("there", 1L),
        KV.of("sue", 2L),
        KV.of("bob", 2L),
        KV.of("", 3L),
        KV.of("ZOW", 1L));

  // Run the pipeline.
  p.run();
}
```
