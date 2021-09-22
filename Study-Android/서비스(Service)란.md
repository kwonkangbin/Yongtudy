## 서비스(Service)란?

UI없이 백그라운드에서 실행되는 기능을 말한다. 예를 들면 노래를 켜고 웹 서핑을 하는 상황이 있다. 우리가 음악을 듣는 앱을 사용할 때 보통 앱에 들어가 노래를 켜고 다른 화면으로 가거나 화면을 끄거나 다른 어플리케이션을 사용한다. 그럼에도 불구하고 노래를 들을 수 있는건 백그라운드에서 노래를 틀어주는 작업을 수행하고 있기 때문이다. 하지만 이러한 작업을 서비스를 사용하지 않고 별도의 스레드를 생성해 백그라운드 작업을 하면 문제가 발생한다. 쓰레드로 백그라운드 작업을 하다가 앱에서 back 키를 눌러 액티비티를 모두 종료할 시 앱 프로세스의 우선순위가 낮아져 LMK(Low Memory Killer)가 프로세스를 강제로 종료 할 수 있다. 이 때 쓰레드도 종료 되므로 안정성에 문제가 생긴다. 그렇다면 서비스는 그냥 쓰레드와 다를까? 먼저, 프로세스 우선순위에 대해 알아보자!

----------------

### 프로세스 우선순위

프로세스가 LMK에 의해 강제 종료될 가능성을 알아보기 위해서 프로세스의 우선순위를 살펴보자. 

1. **포그라운드 프로세스** : 사용자가 상호 작용하는 액티비티를 가지고 있거나 그런 액티비티에 바인딩된 서비스, onCreate/ onStart/ onStartCommand/ onDestroy를 실행 중인 서비스, onReceive를 실행하는 브로드캐스트 리시버에 해당된다.
2. **가시(visible) 프로세스** : 포그라운드 컴포넌트를 가지지 않지만 사용자가 보는 화면에 아직 영향이 있는 프로세스이다.
3. **서비스 프로세스** : startService()로 실행 했지만 위의 카테고리에 들어가지 않는 서비스가 실행 중인 프로세스이다.
4. **백그라운드 프로세스** : 액티비티가 종료되지 않았지만 사용자에게 보이지 않고 (홈 키를 눌러 onStop()까지 불린 태스크) 활성화 된 컴포넌트가 없는 프로세스이다.
5. **빈 프로세스** : 사용자가 백(뒤로가기) 키로 액티비티를 모두 종료하고 활성화된 컴포넌트가 없다면 빈 프로세스가 된다. 우선순위가 낮아서 리소스가 부족하면 가장 먼저 종료된다.

#### 주의 !!!😣

> 서비스의 생명주기 메서드는 메인 쓰레드에서 실행된다. 생명주기 메서드 내에서 오랜 시간동안 작업하면 안된다. 또한 서비스는 앱에서 1개의 인스턴스 밖에 생기지 않으므로 별도로 싱글톤으로 구현하지 않아도 되고 서비스 안에서 사용되는 변수도 별도의 싱글톤 패턴으로 만들 필요가 없다...

--------------------------------

### 서비스 생명주기(Service Life Cycle)

![image](https://user-images.githubusercontent.com/90879448/134164305-04fbb3c1-b096-4f47-a14f-996769cff5a3.png)

 Context에는 서비스를 시작하는 방법으로 startService()와 bindService() 메서드 2가지가 있다. 위에 그림은 각각 생명주기 메서드를 구분한 것이다. 서비스는 보통 Unbound service 와 bound service로 존재한다. 연결을 유지해서 데이터를 주고 받을 수 있는 Bound Service와 시작과 종료에만 관여하는 Unbound Service 이다. Unbound이면서 bound 일 수 있다. 예를 들어, 음악 재생화면이 있을 때 화면을 종료해도 음악을 들을 수 있으려면 Unbound 서비스를 이용해야 한다. 그런데 다시 화면에 진입 할 때 재생 중인 음악 정보를 화면에 보여줘야 한다면 Bound 서비스이기도 해야한다.

--------------------------------------

### Unbound Service

Unbound 서비스는  startService() 메소드로 시작된다. 단, startService를 호출한다고 바로 시작하지 않고 메인 Looper의 MessageQueue에 Message가 들어가서 메인 쓰레드를 쓸 수 있는 시점에 서비스가 시작된다. startService를 처음 호출했을 때는 onCreate() -> onStartCommand()가 실행된다. 이 후에 startService()를 호출하면 onCreate() 메서드는 거치지 않고 onStartCommand() 메소드가 실행된다.

**onCreate()** : Service에 필요한 리소스 등을 준비하는 작업을 함.

**onStartCommand()** : 명령을 매번 처리하는 역할이다.

#### 컴포넌트 간 통신

서비스에서 작업 진행 상황에 따라 액티비티에 메시지를 보내려면 일반적으로 브로드캐스트를 사용합니다. 액티비티에서는 브로드캐스트 리시버를 등록하고 서비스에서는 sendBroadcast()를 실행한다. 또 다른 방식으로는 Service를 시작하는 Intent에 ResultReceiver을 전달하고 서비스에 ResultReciver에 값을 되돌려줄 수 있다. 단방향 메시지 전달이라면 이 방식이 편리하다.

```java
@Override
protected void onCreate(Build savedInstanceState){
    .....
    Intent intent = new Intent(this, MyService.class);
    intent.putExtra(Constant.EXTRA_RECEIVER, resultReceiver);
    startService(intent);
}
private Handler handler = new Handler();
private ResultReceiver resultReceiver = new ResultReceiver(handler){
    @Override
    protected void onReceiveResult(int resultCode, Bundle resultData){
        if(resultCode == Constant.SYNC_COMPLETED){
            .......
        }
    }
}
class MyService extends Servise{
    @Override
    public int onStartCommand(Intent intent, int flags, int startId){
        ....
        final ResultReceiver receiver = intent.getParcelableExtra(Constant.EXTRA_RECEIVER);
        receiver.semd(Constant.SYNC_COMPLETED, null);
        stopSelf();
    }
}
```

#### 서비스 재시작 방식

디바이스 메모리가 부족하거나 포커스를 갖고 있는 액티비티의 시스템 리소스를 복구해야 할 때 안드로이드 시스템은 서비스를 강제 종료 시킬 수 있다. Unbound Service는 강제 종료 후 가능한 한 빨리 시스템에서 서비스를 재시작 할 수 있다. 이 때 onStartCommand의 리턴 값에 따라 동작 방식이 달라진다.

#### OnStartCommand() 리턴 상수

+ START_NOT_STICKY : 강제 종료되면 재시작하지 않는다.
+ START_STICKY : onStartCommand() 메서드의 기본 리턴 값이다. 정상적으로 종료되지 않았을 때 재시작한다. 재시작 시에는 다시 onStartCommand()를 호출하는데 이때 Intent 파라미터가 null로 전달된다. 그렇기 때문에 내부 변수를 사용하는 서비스에 적합하다. 예로 SNS 앱에서 새로운 메시지가 몇 개 왔는지 정기적으로 확인한다면 Intent 파라미터가 전달될 필요가 없다.
+ START_REDELIVER_INTENT : 재시작하면서 onStartCommand()에 Intent를 다시 전달하여 실행한다. 예를 들자면 특정 상점의 상품 목록을 가져온 후 DB에 저장하는 경우가 있다.

#### 서비스 종료

재시작 제어 방식도 중요하지만 불필요하게 재시작하지 않는 것도 중요하다. 작업이 종료되었는데 서비스는 시작된 상태로 남아 불필요하게 메모리를 잡아먹게 된다. 서비스를 종료하는 방법에는 두 가지가 있다.

**stopService**와 **stopSelf**가 있다.

Service의 stopSelf() 메서드는 context의 stopService() 메서드와 역할이 동일하지만 Service 내에서 호출한다는 것이 다르다. 서비스에서 할 일이 끝났으면 stopSelf()를 실행해서 서비스를 명시적으로 종료한다. 이 때 Service()는 onDestroy() 까지 실행된다.

#### 멀티 쓰레드 이슈

여러 곳에서 startService()를 동시에 호출할 수 있다. 이때에는 stopSelfResult()를 실행하는 것이 좋다.

#### IntentService 클래스

동시에 여러 요청을 처리할 필요가 없다면 IntentService를 활용해보자. IntentService는 내부적으로 1개의 백그라운드 쓰레드를 가지고 전달된 Intent를 순차적으로 처리한다. 내부적으로 HandlerThread를 사용한다. IntentService에서는 백그라운드 쓰레드에서 실행되는 onHandlerIntent(Intent) 메서드만 구현하면 된다.







