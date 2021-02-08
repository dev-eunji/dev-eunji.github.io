---
title: "[디자인패턴] 옵서버 패턴 (The Observer Design Pattern)"
categories: [CS]
tags: [CS, 디자인패턴, 옵서버패턴, Observer, Observable]
last_modified_at: 2021-02-08
comments: true
---

> **`데이터 변경`** 이 있을 때, 관심 있어하는 여러 클래스들에게 알리는 방법에 대한 패턴이다.

예를 들어서, 어떤 `이벤트`가 발생할때마다 `친구A`에게 알려준다고 해보자. 아래와 같이 코드를 작성하면 어떨까 생각해보자.

1.  `친구A` 는 이벤트 듣기를 거부하고, 이제 `친구B`가 듣고 싶다고 한다면?
2.  `친구B` ~ `친구Z` 가 듣고 싶다고 한다면?

```kotlin
class Event {
    private var friendA: FriendA? = null
    var name = ""

    fun setFriendA(friendA: FriendA) {
        this.friendA = friendA
    }

    fun occur(name: String) {
        this.name = name
        friendA?.update()
    }
}

class FriendA constructor(private var event: Event) {
    fun update() {
        println("A: ${event.name} event 발생했다!")
    }
}

@Test
fun client() {
    val event = Event()
    event.setFriendA(FriendA(event))
    event.occur("FUN")

    // 실행결과 - "A: FUN event 발생했네!"
}
```

`Event` 클래스를 직접 수정해주어야 하기 때문에 OCP 원칙에 어긋나게 된다.

누구에게 이벤트 발생(변경)여부를 통보하더라도, `Event` 클래스는 변경없이 재사용 하고 싶은 것이 `옵서버 패턴`의 핵심이다.

그렇다면, `이벤트를 발생시키는 클래스`와 `이벤트 발생(변경)이 궁금한 클래스` 로 나누어 볼 수 있을 것이다.
또한, 각각을 추상화 시켜 구체적인 클래스를 서로 알지 못하도록 하자.

<img src="/assets/images/cs/design_pattern/observer_pattern.jpeg" width="500" height="500" />

그렇다면, 위의 Event 예제를 `옵서버 패턴`을 적용하여 수정해보자.

```kotlin
abstract class Subject {
    private var observers = mutableListOf<Observer>()

    fun add(o: Observer) {
        observers.add(o)
    }

    fun remove(o: Observer) {
        observers.remove(o)
    }

    fun notifyObservers() {
        for (o in observers) {
            o.update()
        }
    }
}

interface Observer {
    fun update()
}

class Event : Subject() {
    var name = ""

    fun occur(name: String) {
        this.name = name
        notifyObservers()
    }
}

class FriendA constructor(private var event: Event) : Observer {
    override fun update() {
        println("A: ${event.name} event 발생했네!")
    }
}

class FriendB constructor(private var event: Event) : Observer {
    override fun update() {
        println("B: ${event.name} event 발생했네!")
    }
}

@Test
fun client() {
    val event = Event()
    val friendA = FriendA(event)
    val friendB = FriendB(event)

    event.add(friendA)
    event.occur("FUN")

    event.add(friendB)
    event.occur("HAPPY")

    event.remove(friendA)
    event.occur("COOL")
}
```

예제 코드에서도 알 수 있듯이, 이벤트 발생(변경)여부를 통보받을 클래스들을 `Event` 클래스(`Subject`,`Observable` 클래스)의 수정 없이 쉽게 추가/삭제할 수 있음을 확인할 수 있다.

결론: `옵서버 패턴`은 데이터가 변경되는 클래스(`Observable`)의 수정 없이 데이터 변경을 통보 받을 클래스(`Observer`)를 쉽게 추가 및 삭제할 수 있게 한다.

---

- 참고
  - `java.util` package 의 `Observer`와 `Observable` 의 코드
  - `JAVA객체지향디자인패턴`
