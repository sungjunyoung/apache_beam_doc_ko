### Data encoding and type safety

당신이 pipeline 데이터를 출력하거나 만들 때, 당신의 `PCollection`내의 element들을 어떻게 인코딩할지 지정하고, byte string 으로부터 어떻게 디코딩할지 지정해야 합니다. byte string 은 소스에서 읽거나 sinks에 쓰는 중간 저장용으로 사용됩니다. Beam SDK 는 어떻게 주어진 `PCollection` 의 element 들이 인코딩되고 디코딩 되는지 설명하기 위해서 coder 라는 오브젝트를 사용합니다.

#### Using coders

당신은 전형적으로 외부 소스로부터 당신의 pipeline 에 데이터를 읽을 때 coder 를 구체화할 필요가 있습니다.(혹은 로컬 데이터로부터 pipline 을 만들때) 그리고, 외부 sink 에게 데이터를 출력할 때도 마찬가지 입니다.

Beam SDK 에서, (Java) `Coder`타입은 데이터를 인코딩하고 디코딩하는데 필요한 메소드를 제공합니다. Java SDK 는 Integer, Long, Double, StringUtf8 등과 같은 Java 표준 타입과 동작하는 여러 개의 Coder 서브클래스를 제공합니다. [Coder package](https://github.com/apache/beam/tree/master/sdks/java/core/src/main/java/org/apache/beam/sdk/coders)에서 가능한 Coder 의 서브클래스들을 볼 수 있습니다.

당신이 pipeline 으로 데이터를 읽을 때, Coder는 입력 데이터를 Integer 또는 String 과 같은 언어 타입으로 해석하는 방법을 나타냅니다. 마찬가지로, Coder는 pipeline의 언어 별 타입을 출력 데이터 싱크의 바이트 문자열에 쓰거나 파이프 라인의 중간 데이터를 구체화하는 방법을 나타냅니다.

Beam SDK 는 transform 을 통해 생성되 결과로 나온 `PCollection`을 포함해서 pipeline 내의 모든 `PCollection`에 대해서 coder 를 설정합니다. 대부분의 시간동안, Beam SDK 는 자동으로 출력 `PCollection`에 대한 알맞는 coder 를 추론합니다.

> coder 는 type 과 1:1 관계를 가질 필요는 없습니다. 예를들어, Integer 타입은 다수의 coder 를 가질 수 있고, input 과 output 데이터는 다른 Integer coder 를 사용할 수 있습니다. transform 은 BigEndianIntegerCoder를 사용하는 Integer 타입의 입력 데이터와 VarIntCoder를 사용하는 Integer 타입의 출력 데이터를 가질 수 있습니다.(?)

`PCollection`을 입력하거나 출력할 때 명시적으로 coder를 설정할 수 있습니다. pipeline의 읽기 또는 쓰기 transform을 적용 할 때 `.withCoder` 메서드를 호출하여 코더를 설정합니다.

일반적으로, `PCollection`이 자동으로 선택되지 못할 때 혹은 기본 pipeline 의 coder 보다는 다른 coder 를 사용하기를 원할 때 당신은 `Coder`를 지정합니다. 다음의 예제 코드는 텍스트 파일로부터 숫자들을 읽고, `PCollection`의 결과로 `TextualIntegerCoder` 타입으로 `Coder`를 지정합니다.

```java
PCollection<Integer> numbers =
  p.begin()
  .apply(TextIO.Read.named("ReadNumbers")
    .from("gs://my_bucket/path/to/numbers-*.txt")
    .withCoder(TextualIntegerCoder.of()));
```

`PCollection.setCoder`메서드를 사용해서 존재하는 `PCollection`의 coder 를 지정할 수 있습니다. finalized 된 `PCollection`에서는 `setCoder`를 호출할수 없음에 유의하십시오.

`getCoder` 메소드를 사용함으로서 `PCollection`을 위한 coder 를 얻을 수 있습니다. 이 메소드는 주어진 `PCollection`에 대해서 coder 를 추론하지 못하고, coder 가 지정되지 않는다면, `anIllegalStateException` 오류를 주며 실패할 것입니다.

#### Coder inference and default coders (coder 추론 및 기본 coder)

Beam SDK 는 당신의 pipeline 에 있는 모든 `PCollection`에 대하여 coder 가 필요합니다. 그러나 대부분, 당신은 coder 를 명확하게 지정할 필요는 없습니다. 예를들어, pipeline 의 중간쯤에서 transform 에 의해 생성된 `PCollection`등입니다. 이런 상황에서, Beam SDK 는 PCollection 을 생성하는데 사용된 transform의 입력과 출력으로부터 적절한 coder 를 추측합니다.

각각의 pipeline 오브젝트는 `CoderRegistry`를 가집니다. `CoderRegistry`는 Java 타입을 pipline이 각 타입의 `PCollection`에 사용해야하는 기본 coder에 매핑하는 것입니다.

기본적으로, Java Beam SDK는 transform 의 함수로부터 type 매개변수를 사용하는 출력 `PCollection`의 element 를 위한 `Coder`를 자동으로 추측합니다(`DoFn` 같은). `ParDo`의 경우, 예를들어, `DoFn<Integer, String> function` 같은 경우, 입력 element 로 `Integer` 타입을 받고, `String`타입을 출력합니다. 이런 경우에서, Java SDK 는 자동으로 기본 `Coder`를 출력 `PCollection<String>`에 대한 `Coder`를 추론할 것입니다. (`CoderRegistry` default 는 `StringUtf8`입니다.)

> **Note** 만약 당신이 `Create` transform 을 사용해서 in-memory data 로부터 `PCollection`을 만들었다면, coder 유추나 기본 coder 에 의존할 수 없습니다. 만약 argument list 가 실행시 클래스에 기본 coder 가 등록되어 있지 않는 경우, `Create`는 argument로 아무 타입 정보도 넘길 수 없습니다.

`Create`를 사용할 때, 올바른 coder 라는 것을 확인하는 가장 쉬운 방법은 `Create` transform 을 적용할때, `withCoder`를 동작시키는 것입니다.

**Default coders and the CoderRegistry**

각각의 Pipeline 오브젝트는 그것의 language type 을 기본 coder로 mapping 하는 `CoderRegistry` 오브젝트를 가집니다. 당신은 주어진 타입에 기본 coder 가 무엇인지 보기 위해 `CodeRegistry`를 사용하거나, 주어진 타입에 새로운 기본 coder를 득록하기 위해 사용할 수 있습니다.

`CoderRegistry`는 당신이 Java Beam SDK를 사용해 어떤 pipeline 을 만들건 표준 Java Type 에 맞는 coder 들의 기본 mapping을 가지고 있습니다. 다음의 테이블은 기본 mapping 을 보여줍니다.:

|Java Type	| Default Coder|
|---|---|
|Double	| DoubleCoder|
|Instant	| InstantCoder|
|Integer	| VarIntCoder|
|Iterable	| IterableCoder|
|KV	| KvCoder|
|List	| ListCoder|
|Map	| MapCoder|
|Long	| VarLongCoder|
|String	| StringUtf8Coder|
|TableRow	| TableRowJsonCoder|
|Void	| VoidCoder|
|byte[ ]	| ByteArrayCoder|
|TimestampedValue   |TimestampedValueCoder|

Looking up a default coder

당신은 Java 타입을 위한 기본 Coder 를 결정하기 위해 `CoderRegistry.getDefaultCoder` 메소드를 사용할 수 있습니다. 주어진 pipeline 에서 `Pipeline.getCoderRegistry`를 사용하여 `CoderRegistry`에 접근할 수 있습니다. 따라서 pipeline 단위로 Java 형식에 대한 기본 coder를 결정 (또는 설정) 할 수 있습니다. 즉, "이 pipeline의 경우 Integer 값이 `BigEndianIntegerCoder`를 사용하여 인코딩되었는지 확인해줘." 와 같이 사용할 수 있습니다.

Setting the default coder for a type

특정한 pipeline 을 위한 기본 coder 를 지정하기 위해서는, 당신은 pipeline 의 `CoderRegistry`를 얻고, 수정합니다. 당신은 `CoderRegistry` 오브젝트를 얻기 위해서 `Pipline.getCoderRegistry` 메서드를 사용합니다. 그리고 새로운 `Coder`를 타겟 타입에 등록하기 위해 `CoderRegistry.registerCoder`메서드를 사용합니다.

다음의 예제는 어떻게 기본 Coder 를 결정하는지 보여줍니다. 이 예제에서는, pipeline 의 Integer value 를 위한 `BigEndianIntegerCoder` 입니다.

```java
PipelineOptions options = PipelineOptionsFactory.create();
Pipeline p = Pipeline.create(options);

CoderRegistry cr = p.getCoderRegistry();
cr.registerCoder(Integer.class, BigEndianIntegerCoder.class);
```

Annotating a custom data type with a default coder

만약 당신의 pipeline 프로그램이 커스텀 데이터 타입을 정의했다면, 당신은 그 타입에 사용할 coder 를 지정하기 위해 `@DefaultCoder` 어노테이션을 사용할 수 있습니다. 예를들어, 당신이 `SerializableCoder`를 사용하고 싶고, 커스텀 데이터 타입을 가지고 있다고 칩시다. 당신은 `@DefaultCoder` 어노테이션을 다음과 같이 사용할 수 있습니다.

```java
@DefaultCoder(AvroCoder.class)
public class MyCustomDataType {
  ...
}
```

만약 당신이 당신의 타입에 맞는 커스텀 coder 를 만들었고, 그리고 `@DefaultCoder`어노테이션을 사용하기를 원한다면, 당신의 coder 클래스는 static `Coder.of(Class<T>)`factory method 를 implement 해야합니다.

```java
public class MyCustomCoder implements Coder {
  public static Coder<T> of(Class<T> clazz) {...}
  ...
}

@DefaultCoder(MyCustomCoder.class)
public class MyCustomDataType {
  ...
}
```

> **Note** 이 가이드는 현재 진행중입니다. 가이드를 완성하기 위한 오픈 이슈입니다.([BEAM-193](https://issues.apache.org/jira/browse/BEAM-193))
