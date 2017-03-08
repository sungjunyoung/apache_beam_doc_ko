### Using Flatten and Partition

`Flatten`과 `Partition`은 같은 데이터 타입을 가지고 있는 `PCollection` 오브젝트를 위한 Beam transform 입니다. `Flatten`은 다수의 `PCollection` 오브젝트를 하나의 `PCollection`으로 합칩니다. 그리고 `Partition` 은 하나의 `PCollection` 오브젝트를 작은 고정된 갯수의 `PCollection`으로 쪼갭니다.

#### Flatten
다음의 예제는 어떻게 `Flatten` Transform 으로 다수의 `PCollection` 오브젝트를 합치는지 보여줍니다.

```java
// Flatten takes a PCollectionList of PCollection objects of a given type.
// Returns a single PCollection that contains all of the elements in the PCollection objects in that list.
PCollection<String> pc1 = ...;
PCollection<String> pc2 = ...;
PCollection<String> pc3 = ...;
PCollectionList<String> collections = PCollectionList.of(pc1).and(pc2).and(pc3);

PCollection<String> merged = collections.apply(Flatten.<String>pCollections());
```

**Data encoding in merged collections:**

기본적으로 출력 `PCollection`의 코더(?)는 입력 `PCollectionList`의 첫 번째 `PCollection`에 대한 코더와 같습니다. 입력 `PCollection` 오브젝트가 모두 같은 데이터 타입일 때, 각각 다른 코더를 사용할 수 있습니다.

**Merging windowed collections:**

windowing 전략이 적용된 `PCollection` 오브젝트를 `Flatten`을 사용해 합칠(merge) 때, 당신이 합치길 원하는 모든 `PCollection`오브젝트는 적합한 windowing 전략과 사이즈를 사용해야 합니다. 예를들어, 매 30초마다 시작하는 4분의 sliding window 또는 5분의 fixed window를 사용합니다.

만약 당신의 pipeline이 적합치 않은 window 와 함께 `PCollection`오브젝트를 `Flatten`을 합치려고 한다면, pipeline 이 만들어질때, Beam 은 `IllegalStateException`에러를 낼 것입니다.

#### Partition

`Partition`은 당신이 제공하는 partitioning function 에 따라 `PCollection`의 element 를 분할합니다. partitioning function 은 어떻게 입력 `PCollection`을 각각의 결과적인 `PCollection`의 부분들을 분할할 것인지에 대한 로직을 포함합니다. 파티션의 수는 그래프 작성시 결정해야합니다. 예를들어, command-line 옵션으로 파티션의 갯수를 넘겨줄 수 있습니다. 그러나, 당신은 mid-pipline 내의 파티션 갯수는 결정할 수 없습니다.(파이프 라인 그래프가 생성 된 후에 계산 된 데이터를 기반으로하는.)

다음의 예제는 `PCollection`을 백분위 수 그룹으로 나누는 것을 보여줍니다.

```java
// Provide an int value with the desired number of result partitions, and a PartitionFn that represents the partitioning function.
// In this example, we define the PartitionFn in-line.
// Returns a PCollectionList containing each of the resulting partitions as individual PCollection objects.
PCollection<Student> students = ...;
// Split students up into 10 partitions, by percentile:
PCollectionList<Student> studentsByPercentile =
    students.apply(Partition.of(10, new PartitionFn<Student>() {
        public int partitionFor(Student student, int numPartitions) {
            return student.getPercentile()  // 0..99
                 * numPartitions / 100;
        }}));

// You can extract each partition from the PCollectionList using the get method, as follows:
PCollection<Student> fortiethPercentile = studentsByPercentile.get(4);
```
