## Creating the Pipeline

Pipeline abstraction은 데이터 처리 작업의 모든 데이터와 단계를 캡슐화합니다. Beam 드라이버 프로그램은 일반적으로 Pipeline 객체를 생성 한 다음 파이프 라인의 데이터 세트를 PCollection 및 Transform 으로 조작하기 위한 베이스로 사용합니다.

Beam 을 사용하기 위해서, 당신의 드라이버 프로그램은 Beam SDK class 인 Pipeline (일반적으로 main() 함수) 인스턴스를 생성해야 합니다. 당신이 Pipeline 을 만들때, 옵션 configuration 도 필요합니다. 구성 옵션을 programatically 하게 세팅할 수도 있지만, 가끔은 옵션을 미리 설정하거나 명령 행에서 옵션을 읽는 것이 더 쉽고, 객체를 생성 할 때 Pipeline 객체에 전달하는 것이 더 쉽습니다.

Pipeline 구성 옵션은 파이프 라인 실행 위치를 결정하는 PipelineRunner를 결정합니다. 로컬 혹은 원하는 분산 백엔드를 사용할 수 있습니다. 파이프라인이 실행되는 위치와 지정된 Runner가 필요로하는 항목에 따라 옵션을 사용하여 다르게 지정할 수도 있습니다.

파이프 라인의 구성 옵션을 설정하고 파이프 라인을 생성하려면 PipelineOptions 유형의 객체를 생성하고 `Pipeline.Create()` 에 전달합니다. 이를 수행하는 가장 일반적인 방법은 명령 행에서 인수를 파싱하는 것입니다.

```java
public static void main(String[] args) {
   // Will parse the arguments passed into the application and construct a PipelineOptions
   // Note that --help will print registered options, and --help=PipelineOptionsClassName
   // will print out usage for the specific class.
   PipelineOptions options =
       PipelineOptionsFactory.fromArgs(args).create();

   Pipeline p = Pipeline.create(options);
```

Beam SDK 는 다른 Runner 에 부합하는 PipelineOptions 의 여러 서브클래스들을 포함합니다. 예를들어, DirectPipelineOptions 는 Direct(local) 파이프라인 Runner 를 위한 옵션을, DataflowPipelineOptions 는 Google Cloud Dataflow 를 사용하는 옵션을 포함합니다. 당신은 또한 Beam SDK의 PipelineOptions 클래스를 상속받는 인터페이스를 만들어 당신만의 PipelineOptions를 정의하여 사용할 수도 있습니다.
