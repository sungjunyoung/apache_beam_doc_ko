### Side Inputs and Side Outputs

#### Side Inputs

기본 입력 `PCollection` 외에도 추가 입력을 side input의 형태로 `ParDo` transform에 제공 할 수 있습니다. side input 은 입력에서 element를 처리할 때마다 당신의 `DoFn`이 접근할 수 있는 추가적인 입력입니다. 당신이 side input 을 지정하면, 각 element를 처리하는 동안 `ParDo` transform의 `DoFn`에서 읽을 수 있는 다른 데이터의 보기가 만들어집니다.

side input 은 입력 `PCollection` 의 각각의 element를 프로세싱 할 때, 당신의 `ParDo`가 추가적인 데이터를 주입할 필요하다면 유용합니다. 하지만, 추가적인 데이터는 runtime 에서 정의될 필요가 있습니다(hard cording 이 아니라). 이러한 value 들은 입력 데이터에 의해 결정되고, 당신의 pipeline 의 다른 branch 에 의존합니다.

**Passing side inputs to ParDo:**
```java
// Pass side inputs to your ParDo transform by invoking .withSideInputs.
// Inside your DoFn, access the side input by using the method DoFn.ProcessContext.sideInput.

// The input PCollection to ParDo.
PCollection<String> words = ...;

// A PCollection of word lengths that we'll combine into a single value.
PCollection<Integer> wordLengths = ...; // Singleton PCollection

// Create a singleton PCollectionView from wordLengths using Combine.globally and View.asSingleton.
final PCollectionView<Integer> maxWordLengthCutOffView =
   wordLengths.apply(Combine.globally(new Max.MaxIntFn()).asSingletonView());


// Apply a ParDo that takes maxWordLengthCutOffView as a side input.
PCollection<String> wordsBelowCutOff =
words.apply(ParDo.withSideInputs(maxWordLengthCutOffView)
                  .of(new DoFn<String, String>() {
    public void processElement(ProcessContext c) {
      String word = c.element();
      // In our DoFn, access the side input.
      int lengthCutOff = c.sideInput(maxWordLengthCutOffView);
      if (word.length() <= lengthCutOff) {
        c.output(word);
      }
}}));
```

**Side inputs and windowing:**

window 된 `PCollection`은 무한할 것이며, 그러므로 하나의 값(또는 단일 collection class)으로 압축되지 않습니다. 당신이 window 된 `PCollection`의 `PCollectionView`를 만들 때, `PCollectionView`는 각 윈도우에 대한 entity 를 나타냅니다.(one singleton per window, one list per window 등등)

Beam 은 side input element 를 위한 적절한 window를 찾기 위해 main input element 를 위한 window 를 사용합니다. Beam은 main input element의 window를 side input element의 window에 투영 한 다음 결과 window에서 side input을 사용합니다. 만약 main input 과 side input 이 같은 window를 가진다면, 일치하는 window 를 제공합니다. 하지만, 다른 경우라면, Beam 은 가장 적합한 side input 의 window 를 선택합니다.

예를들어, 만약 main input 이 1분 분량으로 고정된 window를 사용해 windowed 되어 있고, side input 은 1시간 분량의 window 로 되어있다면, Beam 은 main input window 를 설정하고 적절하게 한시간 분량의 input window 에서 side input 값을 선택합니다.

만약 main input element 가 다수의 window 이상에서 존재한다면, 각 window 에 대해 `processElement`는 여러번 호출됩니다. 각각의 `processElement`에 대한 호출은 main input element 에 대해 "최근의" window 를 반영하고, 그러므로 side input 마다 다른 뷰가 나올 수 있습니다.

만약 side input 이 다수의 트리거를 가진다면, Beam 은 가장 최신의 트리거로부터 값을 사용합니다. 단일 global window에서 side input을 사용하고 trigger를 지정할 경우 특히 유용합니다.

#### Side outputs

`ParDo`는 항상 main output인 `PCollection`을 생성하지만, `ParDo`에서 임의의 수의 추가 출력 `PCollection`들을 생성 할 수도 있습니다. 만약 다수의 출력을 선택했다면, 당신의 `ParDo`는 모든 출력 `PCollection`을 묶어 리턴할 것입니다.

**Tags for side outputs:**
```java
// To emit elements to a side output PCollection, create a TupleTag object to identify each collection that your ParDo produces.
// For example, if your ParDo produces three output PCollections (the main output and two side outputs), you must create three TupleTags.
// The following example code shows how to create TupleTags for a ParDo with a main output and two side outputs:

  // Input PCollection to our ParDo.
  PCollection<String> words = ...;

  // The ParDo will filter words whose length is below a cutoff and add them to
  // the main ouput PCollection<String>.
  // If a word is above the cutoff, the ParDo will add the word length to a side output
  // PCollection<Integer>.
  // If a word starts with the string "MARKER", the ParDo will add that word to a different
  // side output PCollection<String>.
  final int wordLengthCutOff = 10;

  // Create the TupleTags for the main and side outputs.
  // Main output.
  final TupleTag<String> wordsBelowCutOffTag =
      new TupleTag<String>(){};
  // Word lengths side output.
  final TupleTag<Integer> wordLengthsAboveCutOffTag =
      new TupleTag<Integer>(){};
  // "MARKER" words side output.
  final TupleTag<String> markedWordsTag =
      new TupleTag<String>(){};

// Passing Output Tags to ParDo:
// After you specify the TupleTags for each of your ParDo outputs, pass the tags to your ParDo by invoking .withOutputTags.
// You pass the tag for the main output first, and then the tags for any side outputs in a TupleTagList.
// Building on our previous example, we pass the three TupleTags (one for the main output and two for the side outputs) to our ParDo.
// Note that all of the outputs (including the main output PCollection) are bundled into the returned PCollectionTuple.

  PCollectionTuple results =
      words.apply(
          ParDo
          // Specify the tag for the main output, wordsBelowCutoffTag.
          .withOutputTags(wordsBelowCutOffTag,
          // Specify the tags for the two side outputs as a TupleTagList.
                          TupleTagList.of(wordLengthsAboveCutOffTag)
                                      .and(markedWordsTag))
          .of(new DoFn<String, String>() {
            // DoFn continues here.
            ...
          }
```

**Emitting to side outputs in your DoFn:**

```java
// Inside your ParDo's DoFn, you can emit an element to a side output by using the method ProcessContext.sideOutput.
// Pass the appropriate TupleTag for the target side output collection when you call ProcessContext.sideOutput.
// After your ParDo, extract the resulting main and side output PCollections from the returned PCollectionTuple.
// Based on the previous example, this shows the DoFn emitting to the main and side outputs.

  .of(new DoFn<String, String>() {
     public void processElement(ProcessContext c) {
       String word = c.element();
       if (word.length() <= wordLengthCutOff) {
         // Emit this short word to the main output.
         c.output(word);
       } else {
         // Emit this long word's length to a side output.
         c.sideOutput(wordLengthsAboveCutOffTag, word.length());
       }
       if (word.startsWith("MARKER")) {
         // Emit this word to a different side output.
         c.sideOutput(markedWordsTag, word);
       }
     }}));
```
