# Observable - First class 

### 들어가며..

RxJava 에서 Observable은 중요한 개념이다.

Rxjava 1에서는 데이터소스를 observable과 Single 클래스로 구성되어있고 Rxjava2에서도 여전히 두 클래스가 존재하지만 Observable, Maybe, Flowable클래스로 구분해 사용한다고 한다. (다 무슨 말인지 아직은 이해가 안된다.)



Observable은 Observer 패턴을 구현한덴다. Observer 패턴은 객체의 상태 변화를 관찰하는 관찰자 목록을 객체에 등록한다. 그리고 상태 변화가 있을 때마다 메서드를 호출해서 객체가 직접 목록의 각 Observer에게 변화를 알려준다.

(ex: 안드로이드에서 버튼에 OnclickListener를 등록 후에 버튼을 누르면 onClick()을 호출하는 것이 Observer 패턴의 예이다.)



RxJava의 Observer은 세가지 알림을 구독자에게 전달한다.

- onNext : 데이터 발행을 알린다.
- onComplete : 데이터 발행을 완료했음을 알린다. onComplete 이후 onNext 이벤트가 발생하면 안된다.
- onError : 에러 발생을 알린다.



Observable 클래스에는 Observable을 생성하는 함수, 결과처리 하는 함수 같이 다양한 함수가 존재한다. 그 함수들을 알아보자!

---------------------------

## just() 함수

just는 Observable을 생성하는 가장 간단한 방법이다. 한개 또는 여러개의 값을 넣을 수 있다. (단, 같은 타입이어야한다.)

사용법

```kotlin
fun main() {
	observable.just(1,2,3,4,5,6).subscribe(System.out::println)
}
```

![img](https://blog.kakaocdn.net/dn/bVayVG/btqy9GWxT6g/SII9TzN0PFi2Iwtf5fbGK0/img.png)

just를 통하여 Observable을 생성하고 데이터 1,2,3,4,5,6을 넣는다. 그리고 subscribe를 넣은 데이터들을 발행한다.





## subscribe() 함수

RxJava를 실질적으로 실행하기 위해서 사용하는 함수는  subscribe() 함수이다. just 와 같은 함수들로 데이터 흐름을 정한 후 subscribe 함수를 호출해야 실제로 데이터를 발행한다.

subscribe() 를 보면 모두 Disposable(직역:일회용) 인터페이스 객체를 리턴하고 있다. Disposable 인터페이스는 dispose() 함수와 isDispoed() 두 가지가 있는데, 

dispose() 함수는 Observable에게 더 이상 데이터를 발행하지 않도록 구독을 해지하는 함수이다.

위에서 Observable이 onComplete() 를 호출할 경우 자동으로 dispose() 를 호출해 Observable과 구독자의 관계를 끊는다. 그러면 Observable이 정상적으로 onComplete로 종료가 되면서 dispose를 호출할 필요가 없다.





## onCreate()  함수

just는 데이터를 인자로 넣으면 자동으로 알림 이벤트가 발생한다. 하지만 create는 onNext, onComplete, onError과 같은 알림을 개발자가 직접 호출해야 한다.

```kotlin
observable.create<Integar> {
	it.onNext(100 as Integer)
	it.onNext(200 as Integer)
	it.onNext(300 as Integer)
}.subscribe { data -> pritln(data) }
```

![img](https://blog.kakaocdn.net/dn/btFUIK/btqzaSBZYtE/I3AbSGyGTo1oJ0sU6iJ3hK/img.png)

위 코드에서 it은 ObservableEmitter을 사용해 람다 형식으로 간소하게 작성하였다. 그리고 as Integer을 하지 않을 경우 

``` kotlin
the integer literal does not conform to the expected type integer
```

라는 error가 발생하기 때문에 사용하였다. 그리고 data 는 변수이름이다. 

onCreate 사용 시 주의할 점은 다음과 같다.

1. Observable이 구독 해지 되었을 때 등록된 콜백을 모두 해제해야한다. 그렇지 않으면 잠재적으로 메모리 누수가 발생한다.
2. 구독자가 구독하는 동안에만 onNext와 onComplete 이벤트를 호출해야 한다.
3. 에러 발생시 오진 onError 이벤트로만 에러를 전달해야한다.
4. 배압을 직접 처리해야한다. (배압은 나중에 알아봅시다.)





## fromArray() 함수

just와 onCreate는 단일 데이터를 다룬다. 그럼 단일 데이터가 아닐 때는 어떻게 해야할까? fromXXX() 함수를 사용하면 된다. 그 중에서 fromArray() 함수를 알아보자.

```java
//java 코드
public static void main(String[] args){ 
    Integer[] arr = {100,200,300}; 
    Observable<Integer> source = Observable.fromArray(arr); 
    source.subscribe(System.out::println); 
}
```

```kotlin
//kotlin 코드
fun main() {
	val arr = arrayof(100, 200, 300)
	val source = arr.toObservable()
	soss.subscribe {
		println(it)
	}
}
```

![img](https://blog.kakaocdn.net/dn/cIC8wa/btqzaYJjHS4/KwYHfmJllIKzBFP5ABaLhK/img.png)

그런데 kotlin의 경우 java와 다르게 fromArray를 안쓰고 toObsevable을 사용하는데, 이는 toObservable의 code를 보면 알 수 있다.

```apl
@CheckReturnValue 
@SchedulerSupport(SchedulerSupport.NONE) 
fun <T : Any> Array<T>.toObservable(): Observable<T> = Observable.fromArray(*this)
```

 이렇게 fromArray를 사용하고 있다.

만약 내가 toObservable을 사용하지 않고 fromArray를 쓰고 싶으면 이렇게 하면 된다.

```kotlin
	val arr = arrayof(100, 200, 300)
	val source = arr.fromArray(*arr)
	soss.subscribe {
		println(it)
	}
```

 이렇게 *arr로 C의 포인터마냥 위치의 데이터를 읽어 올 수 있도록 지정해줘야한다. 그냥 arr만 쓸 경우 그 위치 주소만 나타낸다.





## formlterable() 함수

Interable 인터페이스는 반복자를 반환한다. 즉, iteraor 패턴을 구현한 것으로, 어떤 데이터가 있는지와 값을 얻어오는 것에만 관여한다. (iterator은 다음에 자세히 알아보자.) 

```kotlin
fun main() {
	val list = mutableListOf("Alpha", "Beta", "Gamma")
	val source = list.toObservable()
	source.subscribe(System.out::println)
}
```

위에 fromArray와 마찬가지로 fromlterable 에도 list에 toObservable을 사용했다.

하지만 이때 toObservable은

```apl
@CheckReturnValue 
@SchedulerSupport(SchedulerSupport.NONE) 
fun <T : Any> Iterable<T>.toObservable(): Observable<T> = Observable.fromIterable(this)
```

이렇게 fromlterable을 사용하고 있다. 즉, kotlin에서 fromArray와 fromlterable의 경우 toObservable을 사용하면 타입에 따라 선택 되는 편리함이 있다. 그리고 List뿐만 아니라 다른 타입에도 적용이 가능하다. 

예를 들어 HashSet,     BlockQueue 객체를 구현하는 ArrayBlockingQueue도 가능하다.

<img src="C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20211212142313539.png" alt="image-20211212142313539" style="zoom:80%;" /><img src="C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20211212142335690.png" alt="image-20211212142335690" style="zoom: 67%;" />

(왼쪽이 HashSet 오른쪽이 BlockQueue 객체를 구현하는 ArrayBlockingQueue이다. )

즉, 배열 또는 iterable<E> 인터페이스로 구현하는 객체들은 kotlin에서는 toObservable로 쉽게 Observable을 생성할 수 있다. java의 경우는 배열은 fromArray로 , Iterable<E>인터페이스로 구현하는 객체들은 fromIterable으로 Observable을 생성 할 수 있다.





## fromCallable() 함수

Callable 함수는 자바5에 추가된 함수로, Runable 인터페이스처럼 메서드가 하나고,  인자가 없다는 점에서 비슷하지만 실행결과를 리턴한다는 점에서 차이가 있다. 그리고 Executor 인터페이스에서 인자로 활용되기 때문에 잠재적으로 다른 스레드에서 실행되는 것을 의미한다.  다음은 Callable을 이용한 Observable 생성이다 .(이후 Callable에 대해서 더 자세히 알아보자!)

```kotlin
val callable = Callable<String>() {
	return@Callable "Hello Callable"
}
fun main() {
	Observable.fromCallable(callble).subscribe(System.out::prinln)
    //OR
    Observable.fromCallable{
        "Hello callable"
    }.subscribe(System.out::println)
}
```

![img](https://blog.kakaocdn.net/dn/HwGMT/btqzdxwj1Bk/ZmOE6v4UH9kzPRaQTWTLHK/img.png)



## fromFuture() 함수

Future 인터페이스 역시 fromCallable 과 같이 자바 5에 추가된 API로 계산의 결과를 구할 때 사용된다. 일반적으로 Executor인터페이스를 구현한 클래스에 Callable객체를 인자로 넣어 Future객체를 반환한다.(이후 Future에 대해 더욱 자세히 알아보자!)

```kotlin
fun main() {
	val future = Executors.newSingleThreadExecutor().submit<String> {"Hello future"}
	val source = Observable.fromFuture(future)
	source.subscribe(System.out::println)
}
```

![img](https://blog.kakaocdn.net/dn/eK2EYh/btqzbaW6xi2/x1WGij4crTxSgSMKulLMsK/img.png)

Executors 클래스는 단일 스레드뿐만 아니라 다양한 스레드풀을 지원하는데 , ReactiveX에서는 자기들이 제공하는 스케줄러를 활용하도록 권장한다.





## fromPublisher() 함수

Publisher은 자바9의 표준 Flow API라고 한다.(이후 더 공부하자!)

```kotlin
val publisher = Publisher<String> {
    it.onNext("Hello Observable.fromPublisher")
    it.onComplete()
}

val source = Observable,fromPublisher<String>(Publisher)
source.subscribe(System.out::Println)
```

![img](https://blog.kakaocdn.net/dn/AscAI/btqzb8jLaAt/eFkNA4VkEe8aK9XjkHItv1/img.png)



------------------

## 첫 배움 이후...

많이 어렵다. 생각보다는 쉽지만 기초라 그런것 같다. RXjava를 완벽히 마스터하는 그날까지!! 가보자~ 🔥









