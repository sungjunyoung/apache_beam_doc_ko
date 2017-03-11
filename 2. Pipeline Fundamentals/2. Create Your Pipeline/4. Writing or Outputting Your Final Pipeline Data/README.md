### Writing or Outputting Your Final Pipeline Data

일단 당신의 파이프라인이 그것의 transform 을 모두 적용하면, 당신은 결과를 출력하기를 원할 것입니다. 파이프라인의 마지막 `PCollection` 을 출력하기 위해서, 당신은 그 `PCollection`에 `Write` transform 을 적용합니다. `Write` transform 은 데이터베이스 테이블 같은 외부 데이터 싱크로 `PCollection` 의 element 를 출력할 수 있습니다. 꼭 파이프라인의 끝에 사용하지 않더라도, 당신은 파이프라인의 어떤 지점에서든 `Write` 를 사용할 수 있습니다.

다음의 예제 코드는 텍스트 파일로 `String`의 `PCollection`을 쓰기 위해 어떻게 `TextIO.Write` transform 을 적용하는지 보여줍니다.

```java
PCollection<String> filteredWords = ...;

filteredWords.apply("WriteMyFile", TextIO.Write.to("gs://some/outputData.txt"));
```
