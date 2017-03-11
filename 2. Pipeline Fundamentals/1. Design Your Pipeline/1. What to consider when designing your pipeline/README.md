### What to consider when designing your pipeline

Beam pipeline 을 디자인할 때, 몇개의 기본적인 질문들에 대해 고려해야 합니다.
- **Where is your input data stored?** (당신의 입력 데이터가 어디에 저장되어 있습니까?) 얼마나 많은 입력 데이터가 얼마나 많습니까? 이러한 질문들은 당신의 pipeline 을 만들기 시작할 때 어떤 종류의 `Read` transform 을 적용해야 할 지 결정할 떄 도움을 줄 것입니다.
- **What does your data look like?** (데이터가 어떻게 생겼는지?) 이것은 텍스트일수도, 구조화된 로그 파일 일수도, 데이터베이스 테이블의 row 일수도 있습니다. 몇몇 Beam transform 은 key/value 쌍의 `PCollection`에만 동작합니다. 당신은 당신의 데이터가 어떻게 key 되는지 그리고 당신의 파이프 라인의 `PCollection`들에서 그것을 대표하는 방법을 결정할 필요가 있습니다.
- **What do you want to do with your data?** (당신의 데이터로 무엇을 원하는지?) Beam SDK 의 코어 transform 은 여러 목적이 있습니다. 당신의 데이터를 어떻게 바꿀 것인지에 대해 아는 것은 어떻게 당신이 `ParDo`와 같은 코어 transform 을 만들것인지, 미리 작성된 Beam SDK 를 어떻게 쓸 것인지 결정해 줄 것입니다.
- **What does your output data look like, and where should it go?** (당신의 출력 데이터가 어떻게 생겼는지, 그리고 이것들은 어디로 가는지?) 이것은 당신의 파이프라인의 마지막에 어떤 종류의 `Write` transform 을 쓸것인지 결정하게 해줄 것입니다.
