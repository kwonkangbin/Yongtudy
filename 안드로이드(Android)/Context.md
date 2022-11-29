# Context

### 오늘은 Android Context에 대해 알아보자

> 우리가 제일 많이 쓰는 것 중에 하나이면서 제일 놓치기 쉬운 부분이다. Context의 잘못된 사용은 메모리 누수를 야기할 수 있기 때문에 올바른 Context사용법을 알고 써야하는 것이 중요하다.

<br/>

## Context란?

context는 Activity, 어플리케이션의 정보를 얻기 위해 사용한다.

<br/>

Application의 현재 상태를 갖고 있으며 

안드로이드 시스템에서 제공하는 API들을 사용하기 위해서 사용한다.

<br/>

Activity, Application Class는 Context를 상속받은 클래스라고한다.

<br/>

<br/>

## Context의 종류

Context는 **ApplicationContext**, **ActivityContext**로 나눌 수 있다.

둘 중에 뭐가 어떤, 어디에, 언제, 어떻게 쓰일까 바로 알아보자..!

<br/>

### ApplicationContext

Activity에서 applicationContext라는 프로퍼티를 통해 얻을 수 있는 싱글톤 인스턴스이다.

<br/>

이 Context는 Application Lifecycle과 묶여있어서 현재 Context가 종료되도 계속 Context가 필요할 때, Activity 밖에서 사용해야할 때 쓰이게된다. 이 말은 즉슨, 앱 전역에서 쓰인다는 말이다. 

<br/>

예시로 앱 전역에서 쓰이는 싱글톤 객체를 만들어야할 때 Context를 필요로 한다면 ApplicationContext를 필요로 할 것이다. 이 때 ActivityContext를 쓰게되면 Activity를 참조하기 때문에 메모리 누수가 발생하게 된다.( GarbageCollected 되지 않기 때문에)

<br/>

<br/>

### ActivityContext

ActivityContext는 Activity안에서만 사용이 가능한 Context이다. 

Activity생명주기에 종속되어 있으며, Activity와 생명주기가 같은 객체를 넘겨줄 때 등등 사용된다.

Activity가 없어지면 해당 Activity에 있는 Context도 사라지게 된다.

<br/>

<br/>

## 뽀나스 시간(Bonus Time~✌️)

Toast같은 경우는 ActiviyContext를,

앱의 전역에서 사용되는 싱글톤 객체에겐 ApplicationContext를 주자!

<br/>

ApplicationContext를 쓸 수 있다고 무지성 ApplicationContext를 사용하게 되면 뷰 관련 동작에서 오류를 야기할 수 있다.

<br/>

Acitivity는 GarbageCollected가 가능하지만, Application은 ApplicationContext를 사용한 객체를 메모리 할당해제하지 않으면 메모리 누수될 것이다.

<br/>

<br/>

### 마지막으로!

간단하게! Activity는 ActivityContext, 앱 전역이면 ApplicationContext!

