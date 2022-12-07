# Android App에서 바라본 Socket.io 기초

### Socket.io의 주요 키워드.. 알아보자!

<br/>

## 소켓이란?

일단 소켓이란 무엇일까? 

소켓은 간단히 말해서 서버와 클라이언트가 특정한 Port를 통해서 계속 양방향통신을 하는 것을 말한다.

<br/>

<br/>

## 주요 키워드

### connect,disconnect

connect, disconnect는 말 그대로 소켓을 연결, 연결을 끊는 것이라고 이해하면 된다.

```kotlin
socket.connect()

socket.disconnect()
```

<br/>

### emit

emit이다. emit은 서버에게 event를 보내주는 것이라고 할 수 있다.

```kotlin
socket.emit("create", data)
```

이런 식으로 쓸 수가 있는데 첫 번째 인자에는 event를 써주고 두 번째 인자에는 보낼 data를 넣어준다.

위의 코드를 예시로 들자면 "create"라는 이벤트에 두 번째 인자로 넣어준 data를 서버에 보내(emit)주게 된다.

<br/>

### on

on이다. on은 서버에서 온 event를 받아 이벤트 두 번째 인자의 코드를 실행시켜주는 것이라고 할 수 있다.

```kotlin
private val onErrorMessage: Emitter.Listener = Emitter.Listener {
        val json = JSONObject(it[0].toString())
        status = json.getInt("status")
        code = json.getString("code")
        errorMessage = json.getString("message")
    }

socket.on("error", onErrorMessage)
```

이런 식으로 쓸 수 가 있는데 on안에 event를 써주고 그 뒤에 "error"을 받았을 때 실행될 코드를 작성해준다.

위의 코드를 예시로 들면 "error"이라는 이벤트를 받았을 때 onErrorMessage를 실행시켜준다고 이해하면 된다.

<br/>

<br/>

<br/>

틀린 내용이 있을 수 있을 것 같다.. 피드백은 이슈로 날려주면 좋겠다!🔥

그럼 모두 즐소켓~

