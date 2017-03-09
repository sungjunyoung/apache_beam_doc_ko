## A basic pipeline

가장 간단한 파이프라인은 오퍼레이션의 직렬적인 흐름입니다. 아래의 그림과 같습니다.:

![Figure 1:A linear pipeline](./design-your-pipeline-linear.png)

그러나, 당신의 파이프라인은 더 복잡해질 수 있습니다. 파이프라인은 [Directed Acyclic Graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph)의 스텝을 따릅니다. 이것은 다수의 입력 소스나, 다수의 출력 sink를 가질 수 있으며, 그것의 오퍼레이션(transform) 은 다수의 `PCollection`을 출력할 수 있습니다. 다음의 예제들은 당신의 파이프라인이 가질 수 있는 다양한 모양들을 보여줍니다.
