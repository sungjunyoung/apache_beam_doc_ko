## Overview

Beam을 사용하기 위해서, 당신은 먼저 Beam SDK 중 하나의 클래스를 사용해서 driver program 을 만들어야 합니다. driver progmram 은 당신의 파이프라인을 정의하고, 모든 input 을 포함하며, 변경하고 output 으로 출력합니다. 또한 파이프 라인에 대한 실행 옵션을 설정합니다. (일반적으로 command-line 옵션을 사용하여 전달됩니다.). 그리고, 여기에는 파이프라인 Runner 가 포함되며, 백엔드에서 파이프라인을 실행할 Runner 를 결정합니다.

Beam SDK는 대규모 분산 데이터 처리의 메커니즘을 단순화 하는 많은 abstraction 를 제공합니다. 동일한 Beam abstraction 은 배치 및 스트리밍 데이터 소스 모두에서 작동합니다. Beam 파이프라인을 만들 때 이러한 추상화(abstraction) 측면에서 데이터 처리 작업을 생각해야 합니다. 다음을 포함합니다. :

- **Pipeline** : Pipeline은 전체 데이터 처리 작업을 처음부터 끝까지 캡슐화합니다. 여기에는 입력 데이터 읽기, 해당 데이터 변환 및 출력 데이터 쓰기가 포함됩니다. 모든 빔 드라이버 프로그램은 파이프 라인을 만들어야합니다. 파이프 라인을 생성 할 때 파이프 라인에 실행할 위치와 방법을 알려주는 실행 옵션도 지정해야합니다.
- **PCollection** : PCollection은 빔 파이프라인이 작동하는 분산 데이터 세트를 나타냅니다. 데이터 세트는 바운드 될 수 있습니다. 이 말은 즉, 구독 또는 기타 메커니즘을 통해 지속적으로 업데이트되는(continuously updating source) 소스에서 온다는 의미입니다. 당신의 파이프라인은 일반적으로 외부 데이터 소스로부터 데이터를 읽음으로써 초기 PCollection 을 생성합니다. 하지만 또한, PCollection을 당신의 드라이버 프로그램 내의 in-memory 데이터로부터 만들 수도 있습니다. 그것으로부터, PCollectione 들은 당신의 파이프라인의 각각 단계에서 입력이 되고, 또는 출력이 됩니다.
- **Transform** : Transform 은 당신의 파이프라인에서 데이터 처리 작업이나 단계를 나타냅니다. 모든 Transform 은 하나 이상의 PCollection 오브젝트들을 입력으로 받습니다. 그리고 함수로서 명령을 수행 한 후 하나 이상의 PCollection 오브젝트를 output 으로 출력합니다.
- **I/O Source and Sink** : Beam 은 데이터 읽기와 쓰기에 대해 각각 Source 와 Sink API 들을 제공합니다. Source는 클라우드 파일 저장소 또는 구독 데이터 소스와 같은 외부 소스에서 Beam 파이프 라인으로 데이터를 읽는 데 필요한 코드를 캡슐화합니다. 또한 Sink는 PCollection의 요소를 외부 데이터 싱크에 쓰는 데 필요한 코드를 캡슐화합니다.

일반적인 Beam 드라이버 프로그램은 다음과 같이 동작합니다.

- Pipeline 객체를 만들고, Pipeline Runner 와 같은 외부 옵션을 설정합니다.
- Pipeline 데이터를 위한 초기 PCollection 을 Source API (외부 소스로부터 데이터를 읽어오는)를 사용해 생성하거나, in-memory 데이터로부터 Create transform으로 PCollection 을 만듭니다.
- Transform 을 각각의 PCollection 에 대해 적용합니다. Transform 은 바꾸거나, 필터링하거나, 그룹짓거나, 분석하거나 하는 등의 작업을 PCollection 에 대해 수행할 수 있습니다. 단일 Transform 은 input collection 의 소비 없이 새로운 PCollection output 을 만들어 냅니다. 일반적인 파이프 라인은 처리가 완료 될 때까지 차례로 각 새로운 PCollection에 후속 변환을 적용합니다 (계속적으로).
- 마지막 출력으로, 변환된 (Transform 과정을 거친) PCollection(s) 이 나오게되고, 일반적으로 외부 소스에 데이터를 쓰기 위해 Sink API 를 사용합니다.
- 지정된 파이프 라인 러너를 사용하여 파이프 라인을 실행합니다.

당신이 Beam 드라이버 프로그램을 실행하면, 지정한 파이프라인 Runner가 사용자가 만든 PCollection 개체와 적용한 Transform을 기반으로 파이프 라인의 워크 플로 그래프를 구성합니다. 그런 다음 해당 그래프가 적절한 분산 처리 백 엔드를 사용하여 실행되고 해당 백 엔드에서 비동기 "작업"(또는 동급)을 실시합니다.
