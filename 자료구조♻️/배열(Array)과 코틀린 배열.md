# 배열(Array)

## 원래 내가 알고 있던 배열

배열은 [4] [3] [2] [2] [7] [8] 이런식으로 쓸 수 있고 4의 위치를 찾으라고 하면 0번째 인덱스에 있는 수라고 가리킬 수 있다.

보통 값을 특정 인덱스 안에 저장하는 식으로 많이 쓴다고 알고있다.

## 배열에 대해 조사한 내용

 프로그래밍 언어의 자료형으로 쓰이고 또한 자료구조에 하나이다. 순서대로 번호가 붙은 원소들이 연속적으로 나열된 형태로 존재한다. 또한 배열의 길이를 늘리거나 줄일 수 없고 배열안의 특정값을 삽입, 삭제가 가능하다. Random Access, 즉 임의접근이 가능한 자료구조이다.

### 코틀린에서의 배열 사용법

```kotlin
var itsArray = arrayOf("숫자일",2,34,"hello","World")
```

이런식으로 배열을 사용할 수 있다. arrayOf를 사용하면 각 원소에 들어가는 값의 타입을 상관없이 넣을 수 있게 된다.

```kotlin
var stringArray = arrayOf<String>("Y","O","N","G","J","I","N")

var intArray = arrayOf<Int>(2,0,2,2,0,8,0,2)
```

제네릭을 통해서 타입을 지정해서 배열을 생성할 수도 있고

```kotlin
var intArrayX = intArrayOf(2,0,2,2,0,8,0,2)

var charArrayX = charArrayOf('Y','O','N','G')
//stringArrayOf는 없어요!
```

이렇게 코틀린 내부함수에서도 이런 타입을 자동으로 지정해주는 함수들이 있어서 이렇게 타입 지정 배열을 간편히 생성할 수 있다.

```kotlin
var justArray = Array(배열의 크기,{배열 안의 들어갈 원소 값})

var justArrayX = Array(배열의 크기),{배열 안의 들어갈 원소 값}
```

arrayOf말고도 위 두가지 방식으로 그냥 Array를 통해 이런식으로 배열을 만들 수 있다. 

```kotlin
var justIntArray = Array<Int>(4,{12})

var justStringArray = Array<String>(10),{"helloWorld"}
```

Array도 제너릭을 통한 타입지정으로 배열을 생성 가능하고,

```kotlin
var justIntArrayX = intArray(20,{1})

var justCharArrayX = charArray(7,{'ㅎ'})
```

이런식의 내부함수로 타입지정 배열 생성도 가능하다.



마지막으로 배열 삽입, 삭제하는 법에 대해 알아보자.

```kotlin
array.set(0,100) //set(배열의 인덱스 값, 인덱스에 넣을 것)
array[2] = 1     //array[배열의 인덱스 값] = 인덱스에 넣을 것


array.get(0) //get(배열의 인덱스 값 삭제)
```



## 배열에 대해 새로 알게된 점

배열에 대해서는 학교에서도 배웠고 활용도도 높기 때문에 알고 있었는데 코틀린에서의 배열 문법은 아 이렇게 써도 되는구나 하는 문법도 몇개 있어서 배열에 대해 정확히 알고 코틀린에서의 배열 문법도 배울 수 있었다.

