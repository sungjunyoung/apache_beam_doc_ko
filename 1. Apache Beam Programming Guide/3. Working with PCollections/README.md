## Working with PCollections

PCollection 추상화는 잠재적으로 분산 된, 다수의 요소 (multi-element) 데이터 셋입니다. PCollection 은 "pipeline"의 데이터로 생각할 수 있습니다. Beam 은 이 PCollection 오브젝트를 입력과 출력으로 Transform 하게 됩니다. 이렇게, 만약 당신의 파이프라인에서 데이터를 통해 작업하려고 한다면, 데이터의 형태는 PCollection 이 되어야 합니다.

당신의 Pipeline 을 만들고 나면, 당신은 하나 이상의 PCollection 을 만들고 나서 시작할 필요가 있습니다. 생성한 PCollection은 파이프 라인의 첫 번째 작업에 대한 입력으로 사용됩니다.
