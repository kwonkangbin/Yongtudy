# Observable - Second class

이번 시간에는 Single과 Maybe에 대해 알아보자

-----

## Single

Observable은 무한하게 데이터 발행이 가능하다. 하지만 Single클래스는 1개만 발행되도록 되있다.

![img](https://blog.kakaocdn.net/dn/dB8g0S/btqzcWDHtl8/kd22B103qoMoakIo3c1Ks0/img.png)

이 클래스의 핵심은 발행과 동시에 종료된다는 것이다. 종료 시 onSuccess가 호출된다.





## just() 함수

Single 클래스는 Observable과 똑같은 방법으로 활용된다.

```kotlin
fun main() {
	val single = Single.just("hello single")
	
	single.subscribe{data -> println(data)}
	//OR
	single.subscrieBy(
		onSucess = {Println(it)}
		)
	//OR
	single.subscribe(Consumer<String>(){t -> println(t)})
}
```

위와 같은 방법들로 Single을 만들 수 있다. 

그리고 Single은 아래의 Observable에서 이렇게 활용된다. 

```kotlin
fun main(){
	//single 클래스를 observable로부터 생성한다.
    val source_1 = Observable.just("Hello Single first")
    Single.fromObservable(source_1).subscribe{it -> println(it)}
    //Observable의 just에서 Single함수를 통한 생성이다.
    Observable.just("Hello Single second").single("default item").subscribe{it -> println(it)}
    
    //Observable의 first 함수를 통한 생성이고 첫번째 데이터 이후 onSuccess 호출 후에 종료된다.
    val colors = arrayListOf("Red", "Blue", "Green")
    colors.toObservable().first("default value").subscribe{it -> println(it)}
    //빈 데이터에서 Single 호출 시 defaultItem에 지정한 데이터를 호출한다.
    Observable.empty<Any>().single("default Item").subscribe{it -> println(it)}
    //take를 사용해 1번째까지만 호출하도록 한다. 만약 take를 2로 하면 onError가 호출된다.(take는 이후에 더 공부하자!)
    Observable.just(Order("A_1"), Order("B_2")).take(1).single(Order("Default Id")).subscribeBy(
    onError = {println("onError")},
    onSuccess = {println(it)}
    )
}

data class Order(val id : String) {
    override fun toString() : String {
		return "Order id $id"
    }
}
```



위의 take에서도 설명했듯이 single클래스에 2개의 데이터를 요청해보자

```kotlin
fun main(){ Observable.just("hello hello", "Error").single("default itme").subscribe{it->println(it)} }
```

```ABAP
io.reactivex.exceptions.OnErrorNotImplementedException: The exception was not handled due to missing onError handler in the subscribe() method call. Further reading: https://github.com/ReactiveX/RxJava/wiki/Error-Handling | java.lang.IllegalArgumentException: Sequence contains more than one element! at io.reactivex.internal.functions.Functions$OnErrorMissingConsumer.accept(Functions.java:704) at io.reactivex.internal.functions.Functions$OnErrorMissingConsumer.accept(Functions.java:701) at io.reactivex.internal.observers.ConsumerSingleObserver.onError(ConsumerSingleObserver.java:46) at io.reactivex.internal.operators.observable.ObservableSingleSingle$SingleElementObserver.onNext(ObservableSingleSingle.java:80) at io.reactivex.internal.operators.observable.ObservableFromArray$FromArrayDisposable.run(ObservableFromArray.java:108) at io.reactivex.internal.operators.observable.ObservableFromArray.subscribeActual(ObservableFromArray.java:37) at io.reactivex.Observable.subscribe(Observable.java:12267) at io.reactivex.internal.operators.observable.ObservableSingleSingle.subscribeActual(ObservableSingleSingle.java:35) at io.reactivex.Single.subscribe(Single.java:3603) at io.reactivex.Single.subscribe(Single.java:3589) at io.reactivex.Single.subscribe(Single.java:3559) at Rx_single_testKt.main(rx_single_test.kt:32) at Rx_single_testKt.main(rx_single_test.kt) Caused by: java.lang.IllegalArgumentException: Sequence contains more than one element! ... 10 more Exception in thread "main" io.reactivex.exceptions.OnErrorNotImplementedException: The exception was not handled due to missing onError handler in the subscribe() method call. Further reading: https://github.com/ReactiveX/RxJava/wiki/Error-Handling | java.lang.IllegalArgumentException: Sequence contains more than one element! at io.reactivex.internal.functions.Functions$OnErrorMissingConsumer.accept(Functions.java:704) at io.reactivex.internal.functions.Functions$OnErrorMissingConsumer.accept(Functions.java:701) at io.reactivex.internal.observers.ConsumerSingleObserver.onError(ConsumerSingleObserver.java:46) at io.reactivex.internal.operators.observable.ObservableSingleSingle$SingleElementObserver.onNext(ObservableSingleSingle.java:80) at io.reactivex.internal.operators.observable.ObservableFromArray$FromArrayDisposable.run(ObservableFromArray.java:108) at io.reactivex.internal.operators.observable.ObservableFromArray.subscribeActual(ObservableFromArray.java:37) at io.reactivex.Observable.subscribe(Observable.java:12267) at io.reactivex.internal.operators.observable.ObservableSingleSingle.subscribeActual(ObservableSingleSingle.java:35) at io.reactivex.Single.subscribe(Single.java:3603) at io.reactivex.Single.subscribe(Single.java:3589) at io.reactivex.Single.subscribe(Single.java:3559) at Rx_single_testKt.main(rx_single_test.kt:32) at Rx_single_testKt.main(rx_single_test.kt) Caused by: java.lang.IllegalArgumentException: Sequence contains more than one element! ... 10 more

```

이렇게 에러가 나오게 된다. 이유는 바로 Sequence contains more than one element라고 적혀있다. 즉, 첫번째 onNext이후 두번째 onNext로 데이터를 발행할 때 에러가 발생되는 것이다.





## Maybe

Maybe는 Single 클래스와 비슷하다. 최대 데이터를 하나만 가질 수 있기 때문이다. 하지만 데이터 발행 없이 데이터 발생을 완료 할 수 있다. 즉, Maybe 클래스는 Single클래스에 onComplete 이벤트가 추가된 형태라고 볼 수 있다.

![img](https://blog.kakaocdn.net/dn/bYTLNI/btqzbEEnALs/QBRyz51CvJJyK6MzvxwNM0/img.png)

초록색 체크 모양은 onSuccess 이벤트고 X는 onError, | 는 onComplete 이벤트에 해당한다.

Maybe 객체는  Maybe 클래스로 생성할 수 있지만, 보통 Observable의 특정 연사자를 통해 생성 할 때가 많다. 또한 Maybe객체를 생성할 수 있는 리액티브 연산자에는 elementAt(), firstElement() 등등 있지만 다음에 더 알아보도록 하자!

--------------------------------

## 마치며..

오늘은 Single과 Maybe에 대해서 알아봤다. 둘 다 데이터를 하나만 발행 할 수 있다. Single은 발행과 동시에 종료된다는 것이다.(종료 시onSuccess호출 됨)

 Maybe는 데이터 발행없이 데이터 발생을 완료할 수 있다.  

