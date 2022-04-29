# DI에 대해 알아보자!


## 💉DI가 무엇인가?

일단 존 먼쉬라는 사람이 말한 이 글을 살펴보자.

> **5세 아동을 위한 의존성 주입**
>
> 냉장고에서 스스로 물건을 꺼낼 때 문제가 발생할 수 있습니다. 문을 열어두고 엄마나 아빠가 원하지 않는 것을 얻게 될 수도 있습니다. 우리가 가지고 있지 않거나 만료된 것을 찾고 있을 수도 있습니다.
>
> 당신이 해야 할 일은 "점심과 함께 마실 것이 필요합니다."라고 요구 사항을 말하는 것입니다. 그러면 우리는 당신이 앉을 때 먹을 것이 있는지 확인합니다.

-존 먼쉬, 2009년 10월 28일에-

와우! 여기까지 읽었을 때는 정말 쉽다. 🤓



일단 DI의 D부터 알아보자! (차근차근 You Know?) 

D는 Dependency라고 하고 "의존성"이라는 뜻이다. 보통 객체 둘 중 객체 하나가 다른 객체 하나를 어떤 용도를 위해 사용하는 경우를 Dependency 라고 한다. 

코드를 예시로 들어보자!

```kotlin
class Do {
    fun developing()
}


class LeeYongJin {
    val do = Do()
    fun working() {
        do.developing()
    }
}
```

위 코드에서 이용진(LeeYongJin)과 하는 것(Do)은 의존관계라고 볼 수 있다.

= LeeYongJin객체가 Do 객체에 의존한다.

LeeYongJin이 Do를 가지고 있으므로 이후에 Do가 없어지면 안되게 된다. 만약 Do가 없어진다면 LeeYongJin 객체는 죽는다....(안되ㅣ....일어나..😢) 자! 이처럼 우린 이런 관계를 "Dependence" 즉 "의존" 이라고 한다. 

하지만 여기서 코드를 바꿔야 한다면??

```kotlin
class Dont {
    fun eating()
}


class LeeYongJin {
    val dont = Dont
    fun working() {
        dont.eating()
    }
}
```

기존에 class Do 코드를 class Dont 코드를 바꿔주었다. 바꾼 코드에 맞춰서 LeeYongJin은 기존에 do라고 작성한 코드를 dont라고 바꿔주어야 했다.(물론 developing() -> eating()도 마찬가지) 정말 귀찮고 힘들다.ㅠㅠ 

이런 상황이 닥치면 코드를 유지보수하기 어려울 것이다. (만약 의존관계가 더 많아진다면??)

**그래서! DI가 사용된다.**🤗

🔥와오어아아ㅏ앙

**Dependency Injection**은 의존성 주입을 뜻하며 위에서 설명한 객체끼리의 "의존" 문제점을 위한 디자인 패턴이다. 이젠 외부에서 객체를 생성해서 넣어주게 된다.

```kotlin
interface GoAhead {
    fun promise()
}

class Do : GoAhead {
    override fun promise() {
       	do하는 코드 입력해주기 
    }
}

class Dont : GoAhead {
    override fun promise() {
        dont하는 코드 입력해주기
    }
}

class LeeYongJin (job: GoAhead) {
    private var job: GoAhead
    
    init {
        this.job = job
    }
   	fun changejob(newJob: GoAhead) {
        this.job = newJob
    }
    
    fun working() {
        task.promise()
    }
}
```

위 코드와 같이 의존하는 클래스코드를 직접 짤 필요없이 생성자와 메서드를 통해서 바깥에서 injection (주입)해주어 객체들간의 긴밀도도 줄이고 좀 더 생산적인 코드를 작성할 수 있다. 

이용진(LeeYongJin)이 무슨 일을 하든 ,GoAhead 인터페이스(Interface)를 사용해 만든 Do나 Dont클래스를 만들어서 더 편하게 사용하면 된다.👍

자 우리는 DI (DependecyInjection)에서 객체가 사용할 객체를 각각 만들지 않고 외부(제 3의 객체)에서 사용할 객체를 주입한다. IOC(제어의 역전)이라고 불리는 과정에서 객체의 관계를 맹드는 팩토리 클래스를 사용할 수도 있지만 안드로이드(Android)에서는 Dagger Hilt, Koin같은 좋은 프레임워크를 사용하는 것이 더 편리할 것이다.
