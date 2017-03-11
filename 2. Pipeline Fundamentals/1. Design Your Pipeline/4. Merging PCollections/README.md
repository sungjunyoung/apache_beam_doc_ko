### Merging PCollections

다중의 transform 을 통해 다수의 `PCollection`을 만들었다면, 당신은 아마도 이런 결과들을 최종 결과 `PCollection` 으로 합치고 싶어할 지도 모릅니다. 당신은 다음 중 하나를 사용할 수 있습니다.

- **Flatten** - 당신은 같은 타입의 여러 `PCollection`들을 합치기 위해 Beam SDK 의 `Flatten` transform 을 사용할 수 있습니다.
- **Join** - 당신은 Beam SDK 의 `CoGroupByKey` transform 을 사용하여 두개의 `PCollection` 에 대해 관겨 조인을 수행 할 수 있습니다. `PCollection`은 keyed 되어야 합니다.(key/value 쌍의 collection 이어야 합니다.) 그리고, 그것은 같은 key 타입을 사용하고 있어야 합니다.

아래의 Figure 4에 표시된 예는 위의 Figure2에서 설명한 예제의 연속입니다. 두개의 `PCollection`으로 브랜치 된 후, (A로 시작하는 것과 B 로 시작하는 것) 파이프라인은 두개를 A로 시작하는 것과 B로 시작하는것이 둘다 포함된 하나의 `PCollection` 으로 합칩니다. 여기서 병합되는 `PCollection`는 동일한 타입이기 때문에 `Flatten`을 사용하는 것이 좋습니다.

![Figure 4](./design-your-pipeline-flatten.png)

Figure 4: 여러 PCollection 을 병합하는 파이프라인의 일부
