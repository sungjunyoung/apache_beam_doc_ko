### Using GroupByKey

`GroupByKey`는 key/value 쌍의 collection 에 대해 수행하기 위한 Beam transform 입니다. 이것은 Map / Shuffle / Reduce 스타일 알고리즘의 Shuffle 단계와 유사한 parallel reduction 작업입니다. `GroupByKey`의 입력은 multimap 등과 같은 key/value 쌍의 같은 key를 가지지만 다른 value 를 가지는 collection 입니다. 그런 collection이 주어졌을때, 당신은 `GroupByKey`로 각각의 unique key 에 대한 value 를 묶을 수 있습니다.

`GroupByKey`는 무언가가 공통된 데이터를 aggregate 하기에 좋은 방법입니다. 예를들어, 고객의 주문 레코드를 저장하는 collection 을 가지고 있다고 가정할때, 같은 우편번호로 주문을 묶고 싶을 경우가 있을 것입니다. (key/value에서 , key 는 우편번호, value 는 레코드가 됩니다.)

다음의 간단한 예제 케이스로 `GroupByKey`의 매커니즘을 실펴봅시다. 데이터 세트는 텍스트 파일의 단어와 그 파일이 나타나는 line number로 구성됩니다. 우리는 모든 같은 단어(key)를 공유하는 line number(value)를 특정 단어가 나타나는 텍스트의 모든 부분을 보여면서 그룹짓고 싶습니다.

우리의 입력은 단어가 key 이고, line number 가 value인 key/value 쌍의 PCollection 입니다. 여기에 입력 콜렉션의 key/pair 리스트가 있습니다.

```
cat, 1
dog, 5
and, 1
jump, 3
tree, 2
cat, 5
dog, 2
and, 2
cat, 9
and, 6
...
```

`GroupByKey` 는 같은 key 를 가진 value 를 묶고, 새로운 unique 한 키와 모든 value 의 collection 의 쌍을 출력합니다. 만약 우리가 `GroupByKey`를 우리 입력 collection 에 적용한다면, 출력 collection 은 다음과 같을 것입니다.

```
cat, [1,5,9]
dog, [5,2]
and, [1,2,6]
jump, [3]
tree, [2]
...
```
그러므로, `GroupByKey`는 multimap 으로부터 uni-map으로 transform 됩니다. (multimap : 다수의 key 에 각각 value 가 존재 / uni-map : unique key 에 value collection 존재)

> **A Note on Key/Value Pairs** : Beam은 사용하는 언어와 SDK에 따라 key / value 쌍을 약간 다르게 나타냅니다. Java 용 Beam SDK에서는 KV <K, V> 유형의 객체로 키 / 값 쌍을 나타냅니다. 파이썬에서는 key / value 쌍을 2-tuples로 나타냅니다.
