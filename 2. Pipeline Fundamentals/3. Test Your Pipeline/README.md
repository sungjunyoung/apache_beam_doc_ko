## Test Your Pipeline

파이프라인을 테스트 하는것은 효율적인 데이터 프로세싱 솔루션을 위한 중요한 스텝입니다. 사용자 코드가 원격으로 실행될 파이프 라인 그래프를 구성하는 Beam 모델의 간접적 특성으로 인해 디버깅 실패가 중요하지 않은 작업으로 바뀔 수 있습니다. 종종은 파이프라인의 원경 실행을 디버깅 하는 것보다 로컬 유닛테스트를 진행하는 것이 더 빠르고 심플합니다.

당신이 선택한 runner 위에서 파이프라인을 실행하기 전에, 로컬로 파이프라인을 유닛 테스트 하는 것은 파이프라인 코드를 확인하고 버그를 고치는 데 있어서 최고의 방법입니다. 파이프라인은 당신에게 익숙하고 친숙한 로컬 디버깅 툴을 제공합니다.

당신은 테스팅과 로컬 개발에 유용한 로컬 runner 인 [DirectRunner](https://beam.apache.org/documentation/runners/direct/)를 사용할 수 있습니다.

`DirectRunner` 를 사용해 당신의 파이프라인을 테스트하고 난 후, 작은 단위로 당신이 선택한 runner 에서 테스트 할 수 있습니다. 예를들어, 로컬이나 원격 Flink cluster 에서 Flink 를 사용할 수 있습니다.

Beam SDK 는 당신의 파이프라인 코드를 유닛테스트 하기 위한 로우레벨에서 하이레벨까지 여러가지 방법을 제공합니다.

- [DoFn](https://beam.apache.org/documentation/programming-guide/#transforms-pardo)과 같은 함수에 개별적으로 테스트 가능합니다.
- 유닛으로 [Composite Transform](https://beam.apache.org/documentation/programming-guide/#transforms-composite) 전체를 테스트 할 수 있습니다.
- 전체 파이프라인에 대해 시작부터 끝까지 테스트를 수행 가능합니다.

유닛 테스트 지원을 위해서, Beam Java SDK 는 [testing package](https://github.com/apache/beam/tree/master/sdks/java/core/src/test/java/org/apache/beam/sdk) 내에 여러 테스트를 위한 클래스들을 제공합니다. 당신은 레퍼런스나 가이드를 통해 이 테스트들을 이용 가능합니다.
