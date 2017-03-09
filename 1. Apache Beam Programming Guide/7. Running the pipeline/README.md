### Running the pipeline

pipeline을 실행하기 위해서, `run` 메소드를 사용해야 합니다. 생성한 프로그램은 pipeline runner 에게 pipeline에 대한 명세을 보내고 pipeline runner는 실제 일련의 pipeline 작업을 구성하고 실행합니다. pipeline 들은 기본적으로 비동기적으로 동작합니다.

```java
pipeline.run();
```

blocking 실행을 위해서는, `waitUntilFinish` 메소드를 추가하십시오.

```java
pipeline.run().waitUntilFinish();
```
