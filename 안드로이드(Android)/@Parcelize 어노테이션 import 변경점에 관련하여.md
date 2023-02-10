#  @Parcelize 어노테이션 import 변경점에 관련하여..

### 우선 Android Extensions란?

아주아주 먼옛날 듀드가 안드 개발을 시작할 때 쯔음

android Extension이라는 것이 존재했다….

기존에 findviewbyId를 사용하여 일일히 뷰의 아이디를 찾아주어서 사용하는 어려움이 있었기에 AndroidExtensions에서 제공하는 Synthetics를 사용하여 view의 id를 바로 참조해서 사용할 수 있는 간편한 녀석을 사용했다.

<br/>

물론! Android Extensions에서 Parcelize와 Parceble을 지원했는데 아직 자세히는 모르고 직렬화 역직렬화를 지원하고 보일러플레이트 코드를 줄여준다고 알고 있다. (공부하는대로 내용 추가 예정)

<br/>

### Deprecated된 Android Extensions

하지만! 이 Android Extension이 더 이상 지원되지 않고 **Deprecated**된다고 선언했다!! (왐마~)

그 이유는 전역 네임스페이스 오염과 kotlin에서만 지원, 모듈화 했을 때 합성 속성은 모듈 간 잘 작동하지 않는다.. 등등의 이유로 Synthetic이 사라지고 viewBinding, dataBinding이 대체안으로 나오기 시작했다..

<br/>

그럼! Parcelize는…?

Parcelize의 기능 및 api는 계속해서 지원하기로 결정.

대신, 패키지명이 바뀌었다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/22dc35a1-9dbc-406c-8cca-97e7bbbdaad9/Untitled.png)

위에서 아래로 !