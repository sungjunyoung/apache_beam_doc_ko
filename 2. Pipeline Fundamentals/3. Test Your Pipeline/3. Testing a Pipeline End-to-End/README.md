### Testing a Pipeline End-to-End

당신은 전체 파이프라인을 처음부터 끝까지 테스트하기 위해 Beam SDK 안의 테스트 클래스들(`TestPipeline`과 `PAssert` 등)를할 수 있습니다. 일반적으로, 전체 파이프라인을 테스트 하기 위해서는, 다음을 따라야 합니다.

- 당신의 파이프라인에 모든 입력 데이터의 소스에 대해서, static 한 테스트 입력 데이터를 만듭니다.
- 당신이 예상하는 파이프라인의 최종 `PCollection`의 출력에 맞는 static 테스트 출력 데이터를 만듭니다.
- 파이프라인의 `Read` transform 의 자리에, 당신의 static 입력 데이터로부터 하나 이상의 `PCollection`을 만들기 위해 `Create` transform을 사용합니다.
- 파이프라인의 transform 을 적용합니다.
- 당신의 파이프라인의 `Write` transform 자리에, 최종 `PCollection`의 내용을 확인하기 위해 `PAssert`을 사용합니다.

#### Testing the WordCount Pipeline

다음의 예제 코드는 어떻게 [WordCount example pipeline](https://beam.apache.org/get-started/wordcount-example/)을 테스트하는지 보여줍니다. `WordCount`는 텍스트 입력 파일로부터 열을 릭습니다. 테스트는 몇몇 텍스트 라인을 포함하는 Java의 `List<String>`를 만들고, 초기 `PCollection`을 만들기 위해 `Create` transform 을 사용합니다.

`WordCount`의 마지막 transform은 출력에 적합한 형식화된 단어의 갯수의 `PCollection<String>`을 만들어냅니다. 텍스트 파일로 `PCollection`을 출력하기보다는, `PCollection`의 element 들이 우리가 예상하는 출력 데이터와 일치하는지 확인하기 위해 우리의 테스트 파이프라인은 `PAssert`를 사용합니다.

```java
public class WordCountTest {

    // Our static input data, which will comprise the initial PCollection.
    static final String[] WORDS_ARRAY = new String[] {
      "hi there", "hi", "hi sue bob",
      "hi sue", "", "bob hi"};

    static final List<String> WORDS = Arrays.asList(WORDS_ARRAY);

    // Our static output data, which is the expected data that the final PCollection must match.
    static final String[] COUNTS_ARRAY = new String[] {
        "hi: 5", "there: 1", "sue: 2", "bob: 2"};

    // Example test that tests the pipeline's transforms.

    public void testCountWords() throws Exception {
      Pipeline p = TestPipeline.create();

      // Create a PCollection from the WORDS static input data.
      PCollection<String> input = p.apply(Create.of(WORDS)).setCoder(StringUtf8Coder.of());

      // Run ALL the pipeline's transforms (in this case, the CountWords composite transform).
      PCollection<String> output = input.apply(new CountWords());

      // Assert that the output PCollection matches the COUNTS_ARRAY known static output data.
      PAssert.that(output).containsInAnyOrder(COUNTS_ARRAY);

      // Run the pipeline.
      p.run();
    }
}
```
