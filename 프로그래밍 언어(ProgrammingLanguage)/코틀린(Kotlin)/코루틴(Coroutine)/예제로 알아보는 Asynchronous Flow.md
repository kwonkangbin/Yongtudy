# 예제로 알아보는 Asynchronous Flow

## Flows are cold

플로우는 시퀀스와 유사한 **Cold Stream**이다. Flow 빌더 내부의 코드는 Flow가 collect될 때까지 실행되지 않는다. 이는 아래 예제에서 알 수 있다.

```kotlin
fun simple() : Flow<Int> = flow {
		println("Flow started")
		for (i in 1..3) {
				delay(100)
				emit(i)
		}
}

fun main() = runBlocking<Unit> {
		println("Calling simple function...")
		val flow = simple()
		println("Calling collect...")
		flow.collect { value -> println(value) }
		println("Calling collect again...")
		flow.collect { value -> println(value) }
}

//Result 
Calling simple function...
Calling collect...
Flow started
1 
2
3
Calling collect again...
Flow started 
1
2
3
```

flow는 collect할 때마다 다시 호출되므로 Result에서 Flow started가 collect할 때마다 보이는 모습을 볼 수가 있다.

<br/>

## Flow cancellation basics

```kotlin
fun simple(): Flow<Int> = flow {
		for (i in 1..3) {
				delay(100)
				println("Emitting $i")
				emit(i)
		}
}

fun main() = runBlocking<Unit> {
		withTimeoutOrNull(250) { Timeout after 250ms
				simple().collect { value -> println(value) }
		}
		println("Done")
}

//Result
Emitting 1
1
Emitting 2
2
Done
```

flow는 coroutine의 협력 취소를 준수하며 일시 중지 함수에서 collect가 일시 중지되면 collect를 취소할 수 있다. 위 예제에서는 **withTimeoutOrNull**을 사용하여 250ms가 지나면 수집을 중단하게 만들었다.

<br/>

## Flow builders

flow { … } builder은 flow를 만드는 가장 기본적인 빌더이다. flow를 선언하는 다른 빌더가 존재하는데 flowOf 빌더는 고정된 값을 내보낼 때 사용하고 .asFlow()와 같은 extension을 사용하여 다양한 컬렉션에서 flow로 변환할 수 있다.

```kotlin
//1부터 3을 flow로 변환시킴
(1..3).asFlow().collect { value -> println(value) }

//Result
1
2
3 
```

<br/>

## Intermediate flow operators(flow의 중간 연산자)

```kotlin
suspend fun performRequest(request: Int): String {
		delay(1000) // imitate long-running asynchronous work
		return "response $request"
}

fun main() = runBlocking<Unit> {
		(1..3).asFlow()//a flow of requests
				.map { request -> preformRequest(request) }
				.collect { response -> println(response) }
}

//result
response 1
response 2
response 3
```

중간 연산자는 업스트림 Flow를

름에 적용되며 다운스트림 Flow를 반환한다.

### Transform operator

transform 연산자 중 가장 일반적인 것은 transform이다. transform 연산자를 사용하면 임의의 값을 임의의 횟수만큼 보내는 것이 가능하다.

아래 예제를 보자.

```kotlin
(1..3).asFlow() // a flow of requests
			.transform { request ->
						emit("Making request $request")
						emit( performRequest (request))
			}
			.collect { response -> println (response) }

//Result
Making request 1
response 1
Making request 2
response 2
Making request 3
response 3
```

결과를 보면 알 수 있듯이 transform 연산자를 사용하면 Making request $request같은 임의의 값을 보내고 응답이 오는 것을 볼 수가 있다.

### Size-limiting operators

크기 제한 연산자는 take를 사용하면 되는데 take에 parameter안에 써놓은 값만큼 flow를 collect 했다면 더 이상 flow에서의 collect를 취소한다. Coroutine의 collect 취소는 예외를 발생시켜서 취소되므로 try { … } finally { … } 구문을 사용할 수 있다.

```kotlin
fun numbers() : Flow<Int> = flow {
		try {
				emit(1)
				emit(2)
				println("This line will not excute")
				emit(3)
		} finally {
				println("Finally in numbers")
		}
}

fun main() = runBlocking<Unit> {
		numbers()
				.take(2) // take only the first two
				.collect { value -> println (value) }
}

//Result
1
2
Finally in numbers
```

위 예제를 보면 1과 2까지만 출력하고 flow에서의 collect를 중지한 것을 알 수 있다. take의 parameter에 2를 작성해서 2개의 값만 collect한 것이다.

<br/>

## Terminal flow operators

터미널 연산자는 Flow의 collect를 시작하는 일시 중단 기능이다. collect 연산자는 가장 기본적인 연산자지만 더 쉽게 만들 수 있는 다른 터미널 연산자가 있다. 첫번 째 값을 가져오고 Flow가 단일 값을 내보낼 수 있도록 하는 연산자이다. reduce 및 fold를 사용해서 flow를 값으로 줄인다.

```kotlin
val sum = (1..5).asFlow()
		.map { it * it } // 숫자들의 제곱 from 1 to 5
		.reduce { a, b -> a + b } // sum them (terminal operator)
println(sum)

//Result 
55
```

이렇게 말이다!

<br/>

## Flows are sequential

직역하자면 Flow는 순차적이다라고 해석할 수 있다. Flow가 순차적이다라… 무슨 뜻이지…?  어떤 플로우의 독립된 각각의 collect는 다중 플로우가 사용되지 않는 이상 순차적으로 수행된다. 수집은 종단 연산자를 호출한 코루틴에서 직접 수행되며 기본적으로 새로운 코루틴은 생성하지 않는다. 각각의 방출된 값은 업스트림의 모든 중간 연산자들에 의해 처리되어 다운스트림으로 전달되며 마지막으로 종단 연산자로 전달된다. 예시를 보자

```kotlin
(1..5).asFlow()
		 .filter {
				println("Filter $it")
				it % 2 == 0
			}
			.map {
					println("Map $it)
					"String $it"
			}.collect {
					println("Collect $it")
			}

//Result 
Filter 1
Filter 2
Map 2
Collect String 2
Filter 3
Filter 4
Map 4
Collect String 4
Filter 5
```

<br/>

## Flow Context

Flow의 collect는 항상 호출 코루틴의 컨텍스트에서 발생한다. 예를 들어 simple flow가 있는 경우 아래의 코드는 simple flow의 구현 세부 정보에 관계없이 이 코드의 작성자가 지정한 컨텍스트에서 실행된다.

```kotlin
withContext(context) {
		simple().collect { value -> 
				println(value) // run in the specified context
		}
}
```

flow의 이러한 속성을 컨텍스트 보존이라고 한다.

그래서 기본적으로 Flow { … } 빌더의 코드는 해당 flow의 수집기가 제공하는 컨텍스트에서 실행된다. 예를 들어, simple이 호출된 스레드를 print하고 세 개의 숫자를 내보내는 함수에 대한 구현을 고려해보자

```kotlin
fun simple() : Flow<Int> = flow {
		log("Started simple flow")
		for ( i in 1..3) {
					emit(i)
		}
}

fun main() = runBlocking<Unit> {
		simple().collect { value -> log("Collected $value") }
}

//Result
[main @coroutine#1] Started simple flow
[main @coroutine#1] Collected 1
[main @coroutine#1] Collected 2
[main @coroutine#1] Collected 3
```

### withContext를 사용할 때 흔히 실수하는 부분

오래 실행되는 CPU 소비 코드를 작성할 때 Dispatchers.Default 컨텍스트에서 실행해야하고 UI관련 코드는 Dispatchers.Main 컨텍스트에서 실행해야 한다. 일반적으로 withContext는 코루틴을 사용하면서 코드의 컨텍스트를 변경하는데  사용되지만 빌더의 코드는 컨텍스트 보존 속성을 준수해야 하며 다른 컨텍스트에서 내보낼 수 없다.

```kotlin
fun simple() : Flow<Int> = flow {
		//flow 빌더에서 CPU를 소비하는 코드의 context를 변경하는 잘못된 방법이다.
		kotlinx.coroutines.withContext(Dispatchers.Default) {
				for (i in 1..3) {
						Thread.sleep(100) // 뭔가 CPU를 소비하는 일을 하고 있다 치자
						emit(i) // emit next value
				}
		}
}

fun main() = runBlokcing<Unit> {
		simple().collect { value -> println(value) }
}

//Result 
Exception in thread "main" java.lang.IllegalStateException: Flow .... (error)
```

에러가 뜬다..flow의 불변이 위반됨!

### flowOn operator

이러한 문제점을 flowOn이 해결할 수 있다. flow의 컨텍스트를 변경하는 좋은 연산자이다.

```kotlin
fun simple() : Flow<Int> = flow {
		for (i in 1..3) {
				Thread.sleep(100) // CPU를 많이 사용하고 있다고 가정
				log ("Emitting $i")
				emit (i) //emit next value
		}
}.flowOn(Dispatchers.Default) //flow 빌더 안에서 CPU를 소비하는 코드를 위한 올바른 context 변환 방법

fun main() = runBlocking <Unit> {
		simple().collect { value ->
				log("Collected $value")
		}
}

//Result
[DefaultDispatcher-worker-1 @coroutine#2] Emitting 1
[main @coroutine#1] Collected 1
[DefaultDispatcher-worker-1 @coroutine#2] Emitting 2
[main @coroutine#1] Collected 2
[DefaultDispatcher-worker-1 @coroutine#2] Emitting 3
[main @coroutine#1] Collected 3
```

위 예제를 보면 emit은 coroutine#2에서 emit되고 collect는 coroutine#1에서 collect된다. flowOn연산자는 컨텍스트에서 coroutineDispatcher을 변경해야 할 때 업스트림 flow에 대한 또 다른 코루틴을 **생성**한다.

<br/>

## Buffering

emit하는데도 시간이 걸리고 collect하는데도 시간이 걸린다. 이럴 경우 시간이 얼마나 걸릴까

```kotlin
fun simple() : Flow<Int> = flow {
		for(i in 1..3) {
				delay(100)
				emit(i)
		}
}

fun main() = runBlocking<Unit> {
		val time = measureTimeMillis {
				simple().collect { value ->
						delay(300)
						println(value)
				}
		}
		println("Collected in $time ms")
}

//Result
1
2
3
Collected in 1231ms
```

1231ms정도 걸리고 숫자 1개를 print하는데 각각 400ms정도 소요된다.

flow에서 buffer 연산자를 사용하면 simple을 순차적으로 실행하는 것과 반대로 collect와 동시에 flow의 emit을 실행시킬 수 있다.

아래 코드를 보자

```kotlin
val time = measureTimeMillis {
		simple()
			.buffer() //버퍼를 방출하자, 더 이상 기다리지 마시라!
			.collect { value ->
					delay(300) // 300ms 동안 작업을 처리한다고 가정
					println(value)
			}
}
println("Collected in $time ms")

//Result
1
2
3
Collected in 1059ms
```

첫 번째 숫자에만 100ms를 기다린 다음 각 숫자를 처리하는데 300ms(buffer로 인해 emit할 때 걸리는 시간 제외)만 소비하면 되는 파이프라인을 효과적을로 만들었기 때문에 동일한 결과를 더 빠르게 낼 수 있었다. 이렇게 하면 실행하는  약 1059ms가 걸리게 된다.

### conflate와 collectLatest

작성해놓은 글이 있어 해당 글로 대체합니다.

아래 글로 이동

[Collect와 CollectLatest, 그리고 Conflate 이야기](https://github.com/Leesin0222/Yongtudy/commit/0cf4eaeb1eb71c644e4238412570ef385b2b6062)

<br/>

## Composing multiple flows

여러 flow를 구성하는 방법에는 여러가지가 존재한다.

### Zip

두 flow를 결합하는 zip연산자가 존재한다. 아래 예제를 보자

```kotlin
val nums = (1..3).asFlow() // numbers 1..3
val stars = flowOf("one", "two", "three") //strings
nums.zip(stars) {a, b -> "$a -> $b"} //하나의 string으로 결합한다.
		.collect { println (it) } //collect and print

//Result
1 -> one
2 -> two
3 -> three
```

### Combine

```kotlin
val nums = (1..3).asFlow().onEach { delay(300) } //numbers 1..3 every 300ms
val strs = flowOf("one", "two", "three").onEach { delay(400) } //strings every 400ms
val startTime = System.currenTimeMillis() // remember the start time
nums.zip(strs) { a,b -> "$a -> $b" } //하나의 string으로 결합한다. zip을 사용하여.
		.collect { value -> //collect and print
				println("$value at ${System.currentTimeMillis() - startTime} ms from start")
		}

//Result
1 -> one at 455ms from start 
2 -> two at 856ms from start 
3 -> three at 1258ms from start
```

flow가 가장 최근 작업 값을 나타내는 경우 해당 flow의 가장 최근 값에 의존하는 계산을 수행하고 업스트림 중 하나가 발생할 때마다 다시 계산해야 할 수 있다. flow는 값을 방출한다. 이러한 연산자를 combine이라고 한다.

자, 그럼 여기서 zip대신 combine을 써보자

```kotlin
val nums = (1..3).asFlow().onEach { delay(300) } //numbers 1..3 every 300ms
val strs = flowOf("one", "two", "three").onEach { delay(400) } //strings every 400ms
val startTime = System.currentTimeMillis() //remember the start time
nums.combine(strs) { a, b -> "$a -> $b" } //combine을 통해 하나의 string으로 결합
		.collect{ value -> //collect and print
				println("$value at ${System.currentTimeMillis()-startTime}ms from start")
		}

//Result
1 -> one at 447 ms from start
2 -> one at 649 ms from start
2 -> two at 849 ms from start
3 -> two at 950 ms from start 
3 -> three at 1250 ms from start
```

zip과는 다른 결과를 볼 수 있었다. nums, strs 서로의 flow가 같이 방출되는 모습을 볼 수가 있다.

<br/>

## Flattening flows(평탄화 flow)

```kotlin
fun requestFlow(i: Int): Flow<String> = flow {
		emit("$i: First")
		delay(500) // wait 500ms
		emit("$i: Second")
}
(1..3).asFlow().map { requestFlow(it) }
```

### 이후 Flattening flow부터 내용 추가 작성 필요