### Creating Your Pipeline Object

Beam 프로그램은 `Pipeline` 오브젝트를 생성하면서 시작됩니다.

Beam SDK 에서 각 파이프라인은 `Pipeline` 타입의 명시적 객체로 나타냅니다. 각각의 `Pipeline` 오브젝트는 파이프라인이 작동하는 데이터와 해당 데이터에 적용되는 transform 을 모두 캡슐화하는 독립적인 entity 입니다.

파이프라인을 만들기 위해서, `Pipeline` 오브젝트를 선언하고, 아래 설명되어 있는 몇개의 설정 옵션들을 넘겨줍니다. 당신은 설정 옵션들을 `PipelineOptions` 타입의 오브젝트를 만듦으로서 넘겨줄 수 있습니다. `PipelineOptionsFactory.create()` 메서드를 사용해서 만들 수 있습니다.

```java
// Start by defining the options for the pipeline.
PipelineOptions options = PipelineOptionsFactory.create();

// Then create the pipeline.
Pipeline p = Pipeline.create(options);
```

#### Configuring Pipeline options

당신의 파이프라인을 다르게 설정하기 위해서 파이프라인 옵션을 사용합니다. 예를들어, 당신의 파이프라인을 실행시킬 파이프라인 runner 와 runner 에 필요한 특정한 옵션 등입니다. 당신의 파이프라인 옵션들은 잠재적으로프로젝트 ID 혹은 저장된 파일의 위치 등의 잠재적인 정보들을 포함하고 있습니다.

당신이 선택한 runner 에서 파이프라인을 실행시킬 때, PipelineOptions 의 복사본이 당신의 코드에 사용하능해 질 것입니다. 예를들어, 당신은 DoFn의 Context 로부터 PipelineOptions 를 읽을 수 있습니다.

##### Setting PipelineOptions from Command-Line argument

당신이 `PipelineOptions`오브젝트를 만들고, 필드를 직접적으로 셋팅함으로서 당신의 파이프라인을 설정하면서, Beam SDK 는 command-line argument 를 사용해 `PipelineOptions`안의 필드를 설정할 수 있는 command-line parser 를 포함합니다.

command-line으로부터 옵션을 읽기 위해서는, 다음의 예제와 같이 당신의 `PipelineOptions`객체를 만듭니다.

```java
MyOptions options = PipelineOptionsFactory.fromArgs(args).withValidation().create();
```

이것은 다음의 포맷으로 command-line argument 를 해석할 것입니다.
```
--<option>=<value>
```

> **Note** `.withValidation` 메소드를 추가하면, 필요한 command-line argument와, argument value 들이 타당한지 체크할 것입니다.

이런 방법으로 당신의 `PipelineOptions`를 만드는 것은, command-line argument 로 어떤 옵션이든 당신이 특정짓게 할 수 있습니다.

> **Note** [WordCount example pipeline](https://beam.apache.org/get-started/wordcount-example/)은 command-line options 를 사용해서 어떻게 파이프라인 옵션을 런타입에서 설정하는지 설명합니다.

##### Creating Custom options

표준 `PipelineOptions` 뿐만 아니라 당신만의 커스텀 옵션을 추가할 수 있습니다. 당신만의 옵션을 추가하기 위해서는, 다음의 예제와 같이 각각의 옵션에 대해서 getter 와 setter 메서드를 포함하는 interface 를 정의하십시오

```java
public interface MyOptions extends PipelineOptions {
    String getMyCustomOption();
    void setMyCustomOption(String myCustomOption);
  }
```

또한 당신은 유저가 `--help`를 command-line argument 로 넘겼을 때 나오는 설명을 추가할 수 있고, default value 를 설장할 수 있습니다.

설명을 추가하고 default value 를 설정하려면 다음과 같이 어노테이션을 사용합니다.

```java
public interface MyOptions extends PipelineOptions {
    @Description("My custom command line argument.")
    @Default.String("DEFAULT")
    String getMyCustomOption();
    void setMyCustomOption(String myCustomOption);
  }
```

`PipelineOptionsFactory`와 함께 당신의 interface를 등록하고, `PipelineOptions` 오브젝트를 만들 때, 인터페이스를 패스하는 것을 추천합니다. `PipelineOptionsFactory` 와 함께 당신의 interface 를 등록할 때, `--help` 는 당신의 커스텀 옵션 interface 를 찾고, `--help`커멘드에 그 output 을 추가합니다. `PipelineOptionsFactory`는 역시 당신의 커스텀 옵션이 등록된 모든 옵션들과 적합하는지 검사할 것입니다.

다음의 예제는 `PipelineOptionsFactory`와 함께 어떻게 당신의 커스텀 옵션 interface 를 등록하는지 보여줍니다.

```java
PipelineOptionsFactory.register(MyOptions.class);
MyOptions options = PipelineOptionsFactory.fromArgs(args)
                                                .withValidation()
                                                .as(MyOptions.class);
```

이제 당신은 `--myCustomOption=value`를 command-line argument 로 파이프라인에 넘겨줄 수 있습니다.
