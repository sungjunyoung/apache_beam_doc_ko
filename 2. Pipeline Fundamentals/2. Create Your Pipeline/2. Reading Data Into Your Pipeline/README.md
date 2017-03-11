### Reading Data Into Your Pipeline

당신의 파이프라인의 초기 `PCollection`을 만들기 위해서, 당신은 당신의 파이프라인 오브젝트에 root transform 을 적용해야 합니다. root transform 은 외부 데이터 소스나 당신이 지정한 로컬 데이터로부터 `PCollection`을 만듭니다.

Beam SDK 에는 `Read`와 `Create`라는 두개의 root transform 이 있습니다. `Read` transform 은 텍스트 파일이나 데이터베이스 테이블 같은 외부 소스로부터 데이터를 읽습니다. `Create` transform 은 in-memory `java.util.Collection` 으로부터 `PCollection`을 만듭니다.

다음의 예제 코드는 텍스트 파일로부터 어떻게 `TextIO.Read` root transform 을 ㅈ거용시키는지 보여줍니다. 그 transform 은 `Pipeline` 오브젝트 `p` 에 적용됩니다. 그리고 `PCollection<String>`의 형태로 파이프라인 데이터 셋을 리턴합니다.

```java
PCollection<String> lines = p.apply(
  "ReadLines", TextIO.Read.from("gs://some/inputData.txt"));
```
