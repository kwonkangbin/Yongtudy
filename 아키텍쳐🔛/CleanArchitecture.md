# CleanArchitecture

### 정말 깨끗할까?...

## 아키텍처가 추구하는 것

- UI, DB, 등의 독립성
- 기능 변경 및 확장성 (유지보수)
- 간편한 테스트

클린 아키텍쳐는 Rober C.Martin아조씨가 맹들어주신 아키텍쳐입니다.

아키텍쳐가 추구하는 내용들을 포함하면서 각 계층별로 비즈니스 로직을 캡슐화한

아키텍처이죠.

## 클린 아키텍쳐

![clean](https://user-images.githubusercontent.com/90879448/201897017-5137946b-f376-469a-b0ec-c0128bd9e306.png)

위 그림처럼 

클린 아키텍처는 크게 3개의 모듈로 나눠줄 수 있는데요. (직접 그려서 이상할 수 있어요 ..)

presentation, domain, data로 나눠줄 수 있습니다.

presentation에는 view와 viewModel이,

domain에는 UseCase와 Entity, repository interface가,

data에는 repository구현체, Retrofit과 Room이 존재합니다.

(+ di는 뽀나스? ㅋㅋ)



### data모듈 

data 모듈에서는 api, db, 데이터소스, repostiroy 구현체가 존재합니다.

데이터소스와 상호작용하는 코드가 존재하는 곳인데요. 

data모듈은 domain모듈에 의존합니다.

이때 domain에서 정의한 repository interface를 가져다가 repository를 구현합니다.

(구글에서 추천하는 Repository 패턴이라고 합니다 :)



### domain 모듈

domain 모듈은 앱의 가운데에서 어느 모듈에도 관여하지 않습니다.

domain 모듈에는 **Repository Interface**와 **UseCase**, **Entity **가 존재합니다.

**Repository Interface**는 데이터베이스, remote 서버에 접근을 가능하게 해줍니다.

**Entity**는 특정영역을 나타냅니다 (ex: DTO 등)

**UseCase**는 Entity와 함께 비즈니스 로직을 수행합니다. 

Presentation layer에서 이 UseCase를 끌어다 씁니다.



data모듈에 속한 비즈니스 로직은 제일 중요합니다. 그래서 이 비즈니스 로직을 망치지 않게 하기 위해

domain 모듈은 어떠한 모듈에도 속해있지 않습니다.(의존 X) 

domain모듈은 비즈니스 로직을 한 계층에서 관리하는데 포커스를 맞추고 이를 통해 SRP(단일 책임 원칙)에 부합하는 코딩이 가능해집니다.



### Presentation 모듈

View, ViewModel 등이 있는 모듈이며

Presentation모듈은 Domain모듈을 의존하고 있습니다.

이 때, Domain모듈의 UseCase를 가져다 ViewModel을 구현합니다.





피드백은 환영이며 긴 글 읽어주셔서 감사합니다!✌️













