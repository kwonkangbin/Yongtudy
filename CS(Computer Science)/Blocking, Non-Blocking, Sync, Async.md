# Blocking/Non-Blocking , Sync/Async

### 오늘은 Blocking/NonBlocking, Sync/Async에 대해 알아보자!

<br/>

## 다 알아보자잉~

### Blocking 

A의 작업을 하다가 B의 작업을 하면 A는 쉬고 있다가 B의 작업이 끝난 후에 작업을 이어서 진행한다.

즉, B가 자신의 작업을 마치기 전까지 A에게 제어권을 주지 않고 대기하게 만드는 것이다.

<br/>

### Non-Blocking

A가 작업을 하다가 B가 작업을 하는 순간 A는 작업을 진행한다.

즉, B가 제어권을 바로 Return해서 A에게 다른 일을 할 수 있게 해주는 것이다.

<br/>

### 둘의 차이

작업을 하는 주체가 **제어권**을 가지고 있냐 없냐의 차이이다.

<br/>

<br/>

### Sync(동기)

A가 작업을 하다가 B에게 도움을 요청하고 B는 요청받은 작업은 필요한 수행을하고 A는 기다립니다. 작업을 마친 뒤 B는 A에게 결과를 알려줍니다. 그럼 A는 B에게 받은 결과로 작업을 진행 합니다.

<br/>

### Async(비동기)

A가 작업을 하다가 B에게 도움을 요청하고 A는 자기 할 것을 계속합니다. A는 B에게 결과를 받지 않아도 자기 할 것을 합니다. 나중에 B가 결과를 A에게 보냈을 때 A는 언젠가 B의 결과를 받아 작업을 진행할 것입니다.

<br/>

### 둘의 차이

결과를 B에서 A에게 돌려주었을 때 **순서와 결과**에 관심이 있는지 아닌지로 판단 가능하다.

<br/>

<br/>

<br/>

## 4가지를 섞어보자잉~

###  Blocking + Sync

A가 작업을 하다가 B에게 도움을 요청한다.

B는 제어권을 가지고 있고 A는 그동안 제어권이 B에게 있기 때문에 아무것도 못하고 B에서 결과와 제어권이 와야 작업할 수 있다.



*나머지 Blocking + Async, Non-Blocking + Sync, Non-Blocking + Async는 시간이 없어서 나중에 쓰겠다.* 그럼 유익했다면 빠잉~✌️



