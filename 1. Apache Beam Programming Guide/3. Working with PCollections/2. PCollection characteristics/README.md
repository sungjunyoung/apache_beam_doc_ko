## PCollection Characteristics

PCollection 은 생성된 특정 파이프 라인 개체가 소유합니다.: 다수의 파이프라인은 하나의 PCollection 을 소유할 수 없습니다. 어떤면에서, PCollection은 Java 의 collection 클래스와 같은 기능을합니다. 그러나 PCollection은 몇 가지 중요한 점에서 다를 수 있습니다.

### Element Type

PCollection 의 element 들은 어떤 타입이든 될 수 있습니다만, 모든 element 들은 같은 타입이여야만 합니다. 그러나, 분산 수행을 위해서, Beam 은 각각의 element 를 바이트 문자열로 인코딩 할 수 있어야합니다(그렇게 함으로서 element 들은 분산된 worker 에게 전달될 수 있습니다.). Beam SDK는 일반적으로 사용되는 형식에 대한 기본 제공하는 인코딩을 비롯하여 필요에 따라 사용자 지정 인코딩 지정을 할 수 있는 메커니즘을 제공합니다.

### Immutability (불변성)

PCollection 은 불변성(Immutability)을 가집니다. 한번 만들어지게 되면, 당신은 각각의 element 들을 추가하거나, 삭제하거나, 바꿀 수 없습니다. Beam Transform 은 PCollection 의 각각의 element 에 대해서 프로세싱하고, 새로운 파이프라인 데이터를 만들어 내지만(역시 PCollection 입니다.), 원본의 입력 컬렉션을 사용하거나 수정하지 않습니다.

### Random Access

PCollection 은 각각의 element 에 대해 random access를 제공하지 않습니다. 그러나, Beam Transform 은 PCollection 내의 모든 element 들을 고려합니다.

### Size and boundedness

PCollection 은 크고, 불변하는 element 들의 집합입니다. PCollection 이 가질 수 있는 element 수에 대한 제한은 없습니다. 주어진 PCollection 은 단일 시스템의 메모리에 적합하거나 영구 데이터 저장소가 지원하는 매우 큰 분산 데이터 세트를 나타낼 수 있습니다.

하나의 PCollection 의 사이즈는 제한(bound)되거나 혹은 제한되지 않을(unbounded) 수 있습니다. 제한된 PCollection 은 고정된 사이즈를 가진, 사이즈를 알고 있는 데이터를 나타내고, 제한되지 않는 PCOllection 은 제한이 없는 데이터를 나타냅니다. 제한되거나 혹은 제한되지 않는 PCollection 은 데이터 셋에 달려 있습니다. 데이터 세트의 뭉텅이, 예를들면 파일이나 데이터베이스 같은 경우에는 제한된 PCollection 을 만들어 냅니다. streaming 이나 계속 업데이트 되는 데이터 소스에 대해서, 예를들어 Kafka 나 Pub/Sub 같은 경우는, 명시적으로 그렇지 않다 해도, 제한되지 않는 PCollection 생성합니다.

제한되지 않는 PCollection의 element 에 대한 명령을 수행할 때, Beam 은 계속되는 데이터를 정해진 사이즈로 나누는 Windowing 라고 불리우는 컨셉을 요구합니다. Beam 은 각각의 window 를 bundle 로 수행합니다. 그러면서 데이터는 계속적으로 생성됩니다. 이 window 들은 timestamp와 같은 어떠한 특성으로 결정되게 됩니다.

### Element Timestamps

PCollection 에 있는 각각의 element 들은 고유한 timestamp 에 연관되어 있습니다. 각 요소의 timestamp는 처음에 PCollection을 만드는 [Source](https://beam.apache.org/documentation/programming-guide/#io)에 의해 할당됩니다. unbounded PCollection 을 만드는 Source 들은 종종 각 새로운 element에 element가 읽히거나 추가되었을 때 해당하는 timestamp를 할당합니다.

> **Note** : 고정 데이터 세트에 대해 bounded PCollection을 작성하는 Source도 자동으로 timestamp를 지정하지만 모든 element 에 대해 공통적인 timestamp 를 지정하는 것입니다. (`Long.MIN_VALUE`)

timestamp 는 고유한 시간개념이 있는 element 가 포함된 PCollection에 유용합니다. 만약 당신의 pipeline 이 이벤트의 stream 을 읽는다고 가정할때, (예를들면, 트위터나 다른 소셜 미디어의 메세지) 각각의 element 는 timestamp로 게시 시간을 사용할 것입니다.

당신은 PCollection 의 element 들에게 수동으로 timestamp를 할당할 수 있습니다. element에 고유한 timestamp가 있지만 timestamp 가 요소 자체의 구조에 있는 경우이 작업을 수행 할 수 있습니다. (예를들어, 서버 로그의 "time" 필드 같은 경우) Beam 은 입력으로 PCollection을 취하고 타임 스탬프가 첨부 된 동일한 PCollection을 출력하는 [Transforms](https://beam.apache.org/documentation/programming-guide/#transforms)이 있습니다.: 더 많은 정보를 보려면 [Assigning Timestamps](https://beam.apache.org/documentation/programming-guide/#windowing)를 참고하세요.
