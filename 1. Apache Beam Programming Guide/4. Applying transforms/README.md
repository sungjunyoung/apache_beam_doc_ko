## Applying Transform

Beam SDK 에서, transform 은 당신의 파이프라인 내에서 명령을 뜻합니다. transform 은 PCollection 을 입력으로 취하고, collection 내의 element 에 대해서 특정한 명령을 수행합니다. 그리고 출력으로 새로운 PCollection 을 반환합니다. transform 을 호출하기 위해서는, 당신은 PCollection 을 입력으로 주어야 합니다.

Beam SDK 에서, 각각의 transform 은 apply 라는 generic method 를 가지고 있습니다. 여러 개의 Beam transform 호출은 메소드 체인과 비슷하지만 약간의 차이점이 있습니다.: 당신은 입력 PCollection에 대해서 transfrom 자신을 매개변수로 사용하여 transform 을 적용합니다. 그리고 PCollection을 리턴합니다. 일반적인 형태입니다.

```
[Output PCollection] = [Input PCollection].apply([Transform])
```

Beam 이 PCollection을 위해 generic **apply** method를 사용하기 때문에, 당신은 순차적으로 transform을 체인화 할 수 있으며, 내부에 중첩 된 다른 transform을 포함하는 transform을 적용 할 수도 있습니다. (Beam SDK 에서 **composite transform** 이라고 부릅니다.)

어떻게 당신이 pipeline 의 transform 의 적용하는 가는 당신의 pipeline 구조에 따라 다릅니다. 파이프 라인을 생각하는 가장 좋은 방법은 PCollection 을 node, transform 을 edge 라고 생각할때의 directed acyclic graph(지시 된 비순환 그래프)입니다. 예를들어, 당신은 순차적인 pipeline을 만들기 위해 transform 들을 체이닝하려면:

```
[Final Output PCollection] = [Initial Input PCollection].apply([First Transform])
.apply([Second Transform])
.apply([Third Transform])
```
다음과 같이 작성합니다.

PCollection 은 정의하는 대로 불변이며, 입력 collection 은 transform 으로 인해 절대 소비되거나 변형되지 않는다는 사실을 명심하십시오. 이것은 다음과 같이 같은 입력 PCollection에 여러개의 transform 을 적용할 수 있다는 사실을 뜻합니다.

```
[Output PCollection 1] = [Input PCollection].apply([Transform 1])
[Output PCollection 2] = [Input PCollection].apply([Transform 2])
```

당연히 당신은 당신만의 **composite transform** 을 작성할 수 있습니다. composite transform 은 여러 다른 장소에서 사용되는 재사용 가능한 간단한 단계 시퀀스를 작성하는 데 특히 유용합니다.

### Transforms in the Beam SDK

Beam SDK 에서의 transform 들은  함수 처리 객체의 형태로 generic **processing framework** 를 제공합니다("user code"라고 불리웁니다.). user code는 입력 PCollection의 요소에 적용됩니다. user code의 인스턴스는 pipeline runner와 Beam pipeline을 실행하도록 선택한 백엔드에 따라 클러스터의 많은 다른 worker에 의해 동시에 실행될 수 있습니다. 각각의 worker 에서 동작하는 user code는 출력 element 를 생성하며, 최종적으로 출력 PCollection 에 추가됩니다.

### Core Beam Transforms

Beam 은 다음과 같은 다른 transform 들을 제공합니다.
- `ParDo`
- `GroupByKey`
- `Combine`
- `Flatten` and `Partition`
