### Running Your Pipeline

당신이 파이프라인을 만들고 나면, 파이프라인을 실행시키기 위해 `run` 메서드를 사용합니다. 파이프라인들은 비동기적으로 동작합니다.: 생성한 프로그램은 **pipeline runner** 로 파이프라인에 대한 사양을 전송하고, 파이프라인 runner 는 실제 일련의 파이프라인 작업을 생성하고 실행합니다.

```java
p.run();
```

`run` 메서드는 비동기적입니다. 동기적으로 수행하고 싶으면, `waitUntilFinish` 메서드를 추가하십시오

```java
p.run().waitUntilFinish();
```
