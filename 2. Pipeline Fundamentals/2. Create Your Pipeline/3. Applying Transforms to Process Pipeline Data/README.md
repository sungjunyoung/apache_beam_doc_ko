### Applying Transforms to Process Pipeline Data

당신의 파이프라인에서 transform 을 사용하기 위해서는, 당신이 transform 하기 원하는 `PCollection`에 **apply** 를 해야합니다.

transform을 apply 하기위해서, 당신은 `apply` 메서드를 당신이 프로세싱하길 원하는 각각의 `PCollection` 에 argument 로 필요한 transform 을 넘겨주면서 호출해야 합니다.

Beam SDK 는 `PCollection`에 적용할 수 있는 다수의 다른 transform 을 포함합니다. 일반적인 목적으로 사용할 수 있는 [ParDo](https://beam.apache.org/documentation/programming-guide/#transforms-pardo)나 [Combine](https://beam.apache.org/documentation/programming-guide/#transforms-combine) 같은 코어 transform 을 가지고 있습니다. 또한, 미리 작성된 [composite transform](https://beam.apache.org/documentation/programming-guide/#transforms-composite)도 또한 포함하고 있습니다. 이는 collection 안의 element 들을 카운팅하거나 합치는 등의 여러 유용한 프로세싱 패턴을 한개 이상의 코어 transform 을 조합해 사용할 수 있습니다. 당신은 또한 파이프라인에 적합한 당신만의 더 복잡한 composite transform 을 정의할 수 있습니다.

Beam Java SDK 에서, 각각의 transform 은 `PTransform`을 베이스로 하는 서브클래스들 입니다. 당신이 `apply`를 `PCollection`에 호출할 떄, 당신은 당신이 원하는 argument 로 사용할 `PTransform` 을 전달합니다.

다음의 코드는 어떻게 String 의 `PCollection` 에 transform 을 적용하는지 보여줍니다. transform 은 각각의 문자열을 뒤집고, 뒤집어진 문자열을 포함하는 새로운 `PCollection`을 출력하도록 유저정의된 커스텀 transform 입니다.

입력은 `words` 라고 불리우는 `PCollection<String>` 입니다. 코드는 `ReverseWords`라는 `PTransform` 오브젝트의 인스턴스를 넘겨줍니다. 그리고 `reversedWords`라는 `PCollection<String>` 을 리턴합니다.

```java
PCollection<String> words = ...;

PCollection<String> reversedWords = words.apply(new ReverseWords());
```
