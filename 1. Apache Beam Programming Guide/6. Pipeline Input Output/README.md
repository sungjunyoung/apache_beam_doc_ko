### Pipeline I/O

당신이 pipeline 을 만들 때, 종종 외부 소스로부터의 데이터를 읽을 필요성이 있습니다. pipeline에서 결과 데이터를 유사한 외부 데이터 싱크로 출력 할 수 있습니다. Beam 은 공통된 몇개의 데이터 저장 타입을 위한 입/출력 transform 을 제공합니다. 만약 당신이 built-in transform 에서 지원하지 않는 데이터 저장 포맷을 읽거나 쓰고 싶다면, 당신만의 입/출력 transform 을 implement 할 수 있습니다.

> 당신만의 IO tranform 을 작성하는 가이드는 진행 중에 있습니다. ([BEAM-1-25](https://issues.apache.org/jira/browse/BEAM-1025))

#### Reading input data

Read transform 은 외부 소스로부터 데이터를 읽어들입니다. 그리고, 당신의 파이프라인에 의해 사용될 데이터의 `PCollection`을 리턴합니다. 당신은 pipeline 을 만들 때, 새로운 `PCollection`을 만들도록 언제라도 read transform 을 사용할 수 있습니다. 그것은 당신의 pipeline의 시작에서 가장 공통된 부분입니다.

**Using a read transform:**

```java
PCollection<String> lines = p.apply(TextIO.Read.from("gs://some/inputData.txt"));   
```

#### Writing output Data

Write transform 은 `PCollection`에 있는 데이터를 외부 데이터 소스로 출력합니다. 당신은 아마도 당신의 pipeline 의 끝부분에 마지막 결과를 출력하기 위해 종종 사용할 것입니다. 그러나, 끝부분만이 아니라 pipeline 내에서 언제든지 `PCollection`데이터를 출력하기 위해 write transform을 사용할 수 있습니다.

**Using a Write transform:**

```java
output.apply(TextIO.Write.to("gs://some/outputData"));
```
