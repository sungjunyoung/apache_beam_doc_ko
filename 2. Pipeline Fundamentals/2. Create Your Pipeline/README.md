## Create Your Pipeline

빔 프로그램은 처음부터 끝까지 데이터 처리 파이프 라인을 표현합니다. 이 섹션에서는 Beam SDK의 클래스를 사용하여 파이프 라인을 만드는 방법을 설명합니다. Beam SDK의 클래스를 사용하여 파이프 라인을 생성하려면 프로그램에서 다음과 같은 일반적인 단계를 수행해야합니다.

- `Pipline` 오브젝트를 만듭니다.
- 당신의 파이프라인 데이터를 위한 하나 이상의 `PCollection`을 만들기 위해서 **Read** 혹은 **Create** transform 을 사용합니다.
- 각각의 `PCollection`에 대해 **transform** 을 적용합니다. transform 은 당신의 `PCollection`안의 element 들에 대해 변경하고, 필터링하고, 그룹짓고, 분석하는 등의 프로세스를 수행할 수 있습니다. 각각의 transform 은 당신이 프로세스가 끝날때 까지 추가적인 transform 을 적용할 수 있는 새로운 출력 `PCollection` 을 만들어냅니다.
- transform 된 `PCollection`들을 **Write** 합니다.
- pipeline 을 실행시킵니다.
