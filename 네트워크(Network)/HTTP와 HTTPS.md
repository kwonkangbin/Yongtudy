# HTTP와 HTTPS

### HTTP 2마리 치킨🍗(feat.호시기 두마리)

<br/>

## HTTP란?

일단 HTTP 먼저 알아보자.

HTTP는 Hyper Text Transfer Protocol의 줄임말으로 데이터 통신을 위한 통신 규약(프로토콜)이라고 생각하면 된다. 

기본적으로 서버 - 클라이언트 모델을 따르며 서버가 클라이언트의 정보를 저장하지 않고 요청이 왔을 때 요청에 대한 응답만 처리하는 Stateless한 특성을 갖고 있다. 또한 클라이언트의 요청에 대한 응답을 보낸 후에 연결을 끊는 특성을 가지고 있다.

<br/>

<br/>

## HTTPS란?

그 다음 HTTPS를 알아보자. 

HTTPS는 Hyper Text Transfer Protocol Secure의 줄임말로 데이터가 암호화된 통신 규약(프로토콜)이라 보면 된다.

HTTPS는 HTTP에서 SSL과 통신하고 SSL은 TCP와 통신해서 데이터를 주고 받는다. SSL(SecureSocketLayer)에 연결함으로써 암호화, 증명서 등으로 HTTP보다 안전한 통신을 가능하게 해준다.

<br/>

<br/>

## 그럼 HTTP와 HTTPS의 차이점은 무엇일까

HTTP는 암호화되지 않은 평문데이터를 주고 받기 때문에 제 3자가 데이터를 볼 수 있다.

하지만 HTTPS는 데이터를 암호화해서 주고 받기 때문에 제 3자가 데이터를 볼 수 없다.

HTTP는 TCP와 직접 통신하지만 HTTPS는 SSL과 통신하고 SSL이 TCP와 통신하는 구조이기 때문에 HTTPS가 보다 안전하다.♻️

아! 물론 HTTP가 HTTPS보다 빠르다.⚡ (HTTPS는 데이터가 암호화되는 시간이 걸리기 때문이다.)

<br/>

<br/>

<br/>

<br/>

오늘 글은 여기까지... 피드백은 이슈로~ 모두 빠이✌️

