# RecyclerView 리사이클러뷰

오늘 알아볼 것을 리사이클러뷰입니다! 저는 제가 리사이클러뷰 항상 짜온 코드다보니 익숙하다 생각했는데 익숙함에 속아 기본기를 잊지 말자는 생각으로 시작해볼까요?



## 리사이클러뷰란?

일단 리사이클러뷰에 들어가기 전에 리스트뷰와 비교하고 시작해보자. 리스트뷰는 리스트가 있으면 그 리스트 안의 들어갈 뷰 객체들을 계속 만들어내고 지우고를 반복하는 아주 효율적이지 못한 리스트를 보여주는 뷰이다. (화면에서 리스트를 내리면 아래로 내릴 때마다 맨 위에 있는 뷰 객체를 삭제하고 맨 아래에 뷰 객체를 계속 생성한다는 것이다.) 

반면에 리사이클러뷰는 어떨까? 리사이클러뷰는 리스트뷰와 반대로 효율적이라고 할 수 있다. 리사이클러뷰는 뷰 객체들을 재활용한다. (맨 처음에 화면에 보이는 몇개의 뷰 객체들만 있고 화면에서 리스트를 내리면 뷰 객체들이 재활용되고 뷰 객체 안에서 불러올 데이터들이 갱신되는 구조이다.)



## 리사이클러뷰 기본 사용법

리사이클러뷰의 기본 사용법에 대해 알아볼까?

일단 이 세가지를 기억하자 ViewHolder, Adapter, LayoutManager



### ViewHolder

뷰홀더는 말 그대로 뷰 객체를 기억하는 역할을 한다. 이게 무슨 말이냐 리사이클러뷰는 맨 처음  재활용될 몇개의 뷰 객체들을 기억해야 하는 것이 필요한데 이 역할을 ViewHolder가 수행해준다. (*고마워*🖐️)

### Adapter

어댑터를 살펴볼까? 어댑터의 3부분을 자세히 봐보자 onCreateViewHolder, onBindViewHolder, getItemCount

Adapter이 작동되는 순서는 getItemCount -> onCreateViewHolder -> onBindViewHolder이다.

**getItemCount**

제일 먼저 실행되며 데이터의 길이를 리턴해준다. 만약 데이터의 길이가 ０이라면 데이터가 없다고 판단되어 뷰에 보이지 않게된다。

```kotlin
override fun getItemCount() = rockets.size
```

**onCreateViewHolder**

처음 화면에 보이는 전체 리스트에 아이템들이 한 4개정도 된다면, 위아래 버퍼를 생각해서 5~7개 정도의 뷰 객체가 생성될 것이다. 바로 뷰 객체를 담고 있는 ViewHolder가 생성되는 것이다. 또한onCreateViewHolder함수는 딱 5번에서 7번 정도만 호출된다.

```kotlin
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = RocketDataViewHolder(
    DataBindingUtil.inflate(
        LayoutInflater.from(parent.context),
        R.layout.item_rocket,
        parent,
        false
    )
)
```

**onBindViewHolder**

말 그대로 뷰홀더에 데이터들을 바인딩시켜주는 놈이다.

onBindViewHolder 화면을 내릴 때마다 데이터가 바인딩될 때마다 호출된다.

```kotlin
override fun onBindViewHolder(holder: RocketDataViewHolder, position: Int) {
    holder.itemRocketBinding.tvWaitingName.text = rockets[position].name
    holder.itemRocketBinding.tvReady.text = rockets[position].ready
}
```



----------------------------------------------------

## 인용문

공부하다가 리사이클러뷰에 대한 좋은 말이 있어서 이 문구만 가져와보았다. 

출처:https://wooooooak.github.io/android/2019/03/28/recycler_view/

```apl
첫 화면 이후 스크롤을 아래로 이동시키면 더 이상 onCreateViewHolder는 호출되지 않고 onBind로그만 찍히는 것을 알 수 있다. ViewHolder를 계속 만들지 않고 재사용하기 때문에 데이터만 새롭게 바인딩 해주는 것이다.
```



그럼 오늘은 여기까지 하고 모두 리사이클러뷰에 대한 이해를 하고 쓰길 바란다. 끝~

코드 출처: https://github.com/Leesin0222/VOYAGER/blob/main/voyager/app/src/main/java/com/yongjincompany/voyager/ui/home/adapter/RocketListAdapter.kt
내가 짠 코드에서 가져와보았다.



