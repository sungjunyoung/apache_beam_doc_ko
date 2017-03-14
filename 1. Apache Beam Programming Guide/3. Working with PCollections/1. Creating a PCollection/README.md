## Creating a PCollection

Beam 의 [Source API](https://beam.apache.org/documentation/programming-guide/#io) 를 통해 외부 소스로부터 데이터를 읽어 PCollection 을 생성하거나, 혹은, 당신의 드라이버 프로그램의 in-memory 데이터로 PCollection 을 생성할 수 있습니다.
전자는 일반적으로 생산 파이프 라인이 데이터를 처리하는 방식입니다. Beam의 Source API에는 대규모 클라우드 기반 파일, 데이터베이스 또는 구독(subscription) 서비스와 같은 외부 소스에서 읽을 수 있도록 해주는 어댑터가 포함되어 있습니다. 후자는 주로 테스트 및 디버깅 목적으로 유용합니다.

### Reading from an external Source

외부 소스를 읽어들이기 위해서, [Beam-provided I/O adapters](https://beam.apache.org/documentation/programming-guide/#io)를 사용합니다. 어댑터의 사용법은 다양합니다. 그러나 모든 어댑터들은 외부의 소스를 읽어와 PCollection 을 리턴합니다. PCollection 의 요소는 해당 소스의 데이터 레코드를 나타냅니다.

각각의 데이터 소스 어댑터는 Read Transform 을 가집니다. 읽어들이기 위해서, 당신은 Pipeline 객체에 Transform 을 적용시켜야 합니다. TextIO.Read 를 예로들면, TextIO.Read 는 외부 텍스트 파일을 읽고, PCollection 을 리턴합니다. 이 PCollection 은 String 으로 이루어져 있으며, 각각의 String 은 텍스트파일의 하나의 라인을 나타냅니다. 어떻게 TextIO.Read 를 당신의 Pipeline 에 적용해 PCollection을 만들어 내는지 예제가 있습니다.

```java
public static void main(String[] args) {
    // Create the pipeline.
    PipelineOptions options =
        PipelineOptionsFactory.fromArgs(args).create();
    Pipeline p = Pipeline.create(options);

    PCollection<String> lines = p.apply(
      "ReadMyFile", TextIO.Read.from("protocol://path/to/some/inputData.txt"));
}
```
Beam SDK 로 다른 다양한 데이터 소스를 읽어오는 방법에 대해 더 알아보려면 [section on I/O](https://beam.apache.org/documentation/programming-guide/#io)를 보십시오.

### Creating a PCollection from in-memory Data

in-memory Java Collection 으로부터 PCollection 을 만드려면, 당신은 Beam 에서 제공하는 Create transform 을 이용해야 합니다. 이것은 데이터 어뎁터의 Read 와 비슷하며, 당신은 Pipeline 오브젝트에 직접 Create 를 적용할 수 있습니다.

매개변수로, Create 는 Java 의 Collection 과 Coder 오브젝트를 받습니다. Coder 는 Collection 이 어떻게 [인코딩](https://beam.apache.org/documentation/programming-guide/#pcelementtype)할지를 나타냅니다.

다음 예제는 어떻게 in-memory List 로 부터 PCollection 을 만드는지 보여줍니다. :

```java
public static void main(String[] args) {
    // Create a Java Collection, in this case a List of Strings.
    static final List<String> LINES = Arrays.asList(
      "To be, or not to be: that is the question: ",
      "Whether 'tis nobler in the mind to suffer ",
      "The slings and arrows of outrageous fortune, ",
      "Or to take arms against a sea of troubles, ");

    // Create the pipeline.
    PipelineOptions options =
        PipelineOptionsFactory.fromArgs(args).create();
    Pipeline p = Pipeline.create(options);

    // Apply Create, passing the list and the coder, to create the PCollection.
    p.apply(Create.of(LINES)).setCoder(StringUtf8Coder.of())
}
```
