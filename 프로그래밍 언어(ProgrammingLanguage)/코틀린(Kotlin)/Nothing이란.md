# Nothing

### 아무것도 아니야...

최근에 면접을 봤다.... 난 아직 부족하고 많이 떨었다는 말로 글을 시작하겠다...😥

<br/>

## Nothing.. 뭔가요?

Nothing이라는 것이 존재하는지 몰랐다가 최근에 유튜브 숏츠를 보다가 우연히 안드 유튜버가 Nothing을 소개하는 영상을 봤다.

*흠... 지금에서야 공부하는 나를 때려라...👀* 

암튼! 코틀린 공식문서에서 소개하고 있는 Nothing을 보자!

```kotlin
public final class Nothing private constructor() {}
```

내부 코드가 이렇게 생겼다... 오우! 짧,,,짧다..

Nothing은 존재하지 않은 값을 나타내고 싶을 때 사용하는 자료형(타입)이다.

Nothing은 private constructor, 즉 비공개 생성자로 정의되어있기 때문에 생성자 안에 파라미터를 못 쓴다.

또한 반환하는 것도 없기 때문에 진짜 말 그래로 아무것도.. 없다.

그럼 어떠한 값도 얻을 수 없고 생성자도 못 쓴다면 왜 쓰는 것이지..? 

<br/>

<br/>

## Nothing... 이럴 때 사용해요!

대표적으로 2가지 상황에서 사용됩니다.

+ Exception을 던져줄 때
+ 아무것도 반환되지 않는 경우

아래 예시를 볼까요?😎 (급친절한 말투로)

```kotlin
//1. Exception을 던져주는 예시
fun main(){
  	a()
}

fun a(): Nothing {
  throw IllegalArgumentException()
}

//2. 아무것도 반환될 수가 없는 경우
fun go(): Nothing {
  while(true){
     println("I'm Nothing")
  }
}
```

예시 코드를 보면 알 수 있듯이 Nothing은 throw를 함수가 종류되는 것으로 보지 않기 때문에 return으로 받아들이지 않는다. 그래서 저 코드(1)를 실행했을 때 IllegalArgumentException을 실행시키며 에러가 나게된다.

코드(2)를 보자 음.... 무한루프다. 반환이 아무것도 되지 않을 것이다. Nothing을 쓰자...(?...???????)

말 그대로다. 반환이 아무것도 되지 않으니 Nothing을 붙혀주는 것이다..





----------------------------

### 잠깐... 오늘 면접을 못본 것 같아서 너무 슬프다.. 이후 부분은 내일 이어서 쓰겠다..모두 빠이...✌️

