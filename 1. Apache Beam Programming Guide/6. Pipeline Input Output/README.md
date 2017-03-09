### Pipeline I/O

당신이 pipeline 을 만들 때, 종종 외부 소스로부터의 데이터를 읽을 필요성이 있습니다. pipeline에서 결과 데이터를 유사한 외부 데이터 싱크로 출력 할 수 있습니다. Beam 은 공통된 몇개의 데이터 저장 타입을 위한 입/출력 transform 을 제공합니다. 만약 당신이 built-in transform 에서 지원하지 않는 데이터 저장 포맷을 읽거나 쓰고 싶다면, 당신만의 입/출력 transform 을 implement 할 수 있습니다.

> 당신만의 IO tranform 을 작성하는 가이드는 진행 중에 있습니다. ([BEAM-1-25](https://issues.apache.org/jira/browse/BEAM-1025))

#### Reading input data

Read transform 은 외부 소스로부터 데이터를 읽어들입니다. 그리고, 당신의 파이프라인에 의해 사용될 데이터의 `PCollection`을 리턴합니다. 당신은 pipeline 을 만들 때, 새로운 `PCollection`을 만들도록 언제라도 read transform 을 사용할 수 있습니다. 그것은 당신의 pipeline의 시작에서 가장 공통된 부분입니다.

**Using a read transform:**

```java
PCollection<String> lines = p.apply(TextIO.Read.from("gs://some/inputData.txt"));   
```

#### Writing output Data

Write transform 은 `PCollection`에 있는 데이터를 외부 데이터 소스로 출력합니다. 당신은 아마도 당신의 pipeline 의 끝부분에 마지막 결과를 출력하기 위해 종종 사용할 것입니다. 그러나, 끝부분만이 아니라 pipeline 내에서 언제든지 `PCollection`데이터를 출력하기 위해 write transform을 사용할 수 있습니다.

**Using a Write transform:**

```java
output.apply(TextIO.Write.to("gs://some/outputData"));
```

#### File-based input and output data

**Reading from multiple locations:**

많은 read transform은 사용자가 제공하는 glob 연산자와 일치하는 여러 입력 파일에서 읽기를 지원합니다. glob 연산자는 파일 시스템마다 다르며 파일 시스템 고유의 일관된 모델을 따르는 것에 유의하십시오. 다음의 TextIO 예제는 "input-" prefix 와 ".csv" suffix 에 일치하는 파일을 찾기 위해 주어진 위치에서 glob 연산자(\*)를 사용합니다.

```java
p.apply(“ReadFromText”,
    TextIO.Read.from("protocol://my_bucket/path/to/input-*.csv");
```

다른 소스로부터 단일 `PCollection`으로 데이터를 읽기 위해서는, 각각 독립적으로 변환한 다음 [Flatten](https://beam.apache.org/documentation/programming-guide/#transforms-flatten-partition) transform 을 사용하여 단일 `PCollection`을 만듭니다.

**Writing from multiple locations:**

파일 기반의 출력 데이터를 위해선, write transform 은 기본적으로 여러 출력 파일들을 만듭니다. 당신이 출력 파일 이름을 write transform 으로 넘겨주면, 파일 이름은 모든 출력 파일들에 대한 prefix로 사용됩니다. 당신은 각각의 파일에 대해 suffix도 지정할 수 있습니다.

다음의 예제는 특정 위치에 다수의 출력 파일을 쓰는 것입니다. 각각의 파일들은 "numbers"라는 prefix 를 가지며, suffix 로 ".csv"가 지정됩니다.

```java
records.apply("WriteToText",
    TextIO.Write.to("protocol://my_bucket/path/to/numbers")
                .withSuffix(".csv"));
```

#### Beam-provied I/O APIs

Beam 지원 I/O API에 대한 언어 별 소스 코드 디렉토리를 참조하십시오. 앞으로 이러한 I/O 소스 각각에 대한 특정 문서가 추가 될 것입니다. ([BEAM-1054](https://issues.apache.org/jira/browse/BEAM-1054))

|  Language |  File-based | Messaging	  | Database  |
|---|---|---|---|
| Java  | [AvroIO](https://github.com/apache/beam/blob/master/sdks/java/core/src/main/java/org/apache/beam/sdk/io/AvroIO.java) <br> [HDFS](https://github.com/apache/beam/tree/master/sdks/java/io/hdfs) <br> [TextIO](https://github.com/apache/beam/blob/master/sdks/java/core/src/main/java/org/apache/beam/sdk/io/TextIO.java) <br> [XML](https://github.com/apache/beam/tree/master/sdks/java/core/src/main/java/org/apache/beam/sdk/io)<br>| [JMS](https://github.com/apache/beam/tree/master/sdks/java/io/jms) <br> [Kafka](https://github.com/apache/beam/tree/master/sdks/java/io/kafka) <br> [Kinesis](https://github.com/apache/beam/tree/master/sdks/java/io/kinesis) <br> [Google Cloud PubSub](https://github.com/apache/beam/tree/master/sdks/java/core/src/main/java/org/apache/beam/sdk/io) <br> | [Apache Hbase](https://github.com/apache/beam/tree/master/sdks/java/io/hbase) <br> [MongoDB](https://github.com/apache/beam/tree/master/sdks/java/io/mongodb) <br> [JDBC](https://github.com/apache/beam/tree/master/sdks/java/io/jdbc) <br> [Google BigQuery](https://github.com/apache/beam/tree/master/sdks/java/io/google-cloud-platform/src/main/java/org/apache/beam/sdk/io/gcp/bigquery) <br> [Google Cloud Bigtable](https://github.com/apache/beam/tree/master/sdks/java/io/google-cloud-platform/src/main/java/org/apache/beam/sdk/io/gcp/bigtable) <br> [Google Cloud Datastore](https://github.com/apache/beam/tree/master/sdks/java/io/google-cloud-platform/src/main/java/org/apache/beam/sdk/io/gcp/datastore) <br>  |
|  Python | [avroio](https://github.com/apache/beam/blob/master/sdks/python/apache_beam/io/avroio.py) <br> [textio](https://github.com/apache/beam/blob/master/sdks/python/apache_beam/io/textio.py) <br>  |   | [Google BigQuery](https://github.com/apache/beam/blob/master/sdks/python/apache_beam/io/gcp/bigquery.py) <br> [Google Cloud Datastore](https://github.com/apache/beam/tree/master/sdks/python/apache_beam/io/gcp/datastore) <br>  |
