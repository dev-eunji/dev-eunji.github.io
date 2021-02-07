---
title: "[Design Pattern] 전략 패턴 (The Strategy Design Pattern)"
categories: [CS]
tags: [CS, UML, 디자인패턴, 전략패턴, strategy, strategy pattern]
last_modified_at: 2021-02-06
comments: true
---

## 전략 패턴 (The Strategy Design Pattern)

> `JAVA객체지향디자인패턴` 책 및 여러 블로그들의 예제들을 보고 kotlin 으로 전략 패턴을 적용해보면서 작성한 포스트 입니다.

`전략 패턴` 이라는 이름에서도 알 수 있듯이 전략, 즉 자주 변경될 수 있는 **알고리즘** 등을 쉽게 수정 및 추가가 가능하도록 하는 디자인 패턴이다.

#### 1. 상속을 통한 구현

게임 캐릭터를 디자인한다고 생각해보자. 이때, 게임 캐릭터는 움직일 수 있어야하고, 말할 수 있어야 한다고 가정하자.
**상속**만으로 구현할 경우, `TypeB`의 움직이는 방법을 `TypeA`와 동일하게 변경하고 싶다면..

1. `TypeB`에 기존 구현되어있는 `move()` 을 직접 수정해주어야 하기 때문에, OCP(open-closed principle)을 위배하게 된다.

2. `TypeA` 의 `move()` 구현과 `TypeB`의 `move()` 구현이 중복된다.

##### 코드

```kotlin
abstract class Character(private var name: String) {
    abstract fun move()
    abstract fun speak()
}

class TypeA(name: String) : Character(name) {
    override fun move() {
        print("WALK")
    }

    override fun speak() {
        print("abc")
    }
}

class TypeB(name: String) : Character(name) {
    override fun move() {
        print("RUN")
    }

    override fun speak() {
        print("가나다")
    }
}

@Test
fun testInheritance() {
    val typeA: Character = TypeA("철수")
    typeA.move()
    typeA.speak()
}
```

#### 2. 전략 패턴

<img src="/assets/images/cs/design_pattern/strategy_pattern.jpeg" width="500" height="500" />

- 참고: `Context` 가 `abstract class` 가 아닐 수 있으며, 이경우 직접 strategy 를 `Context` 에 직접 세팅할 수 있다.

1. 변화이 잦은 부분(`move()`, `attack()`)과 그렇지 않은 부분(`TaekwonV`, `Atom`)을 구분하여
   추가는 쉽고 기존 코드의 변경은 필요하지 않은 디자인을 해야한다.

2. **집약관계**를 사용하여, 동적으로 `Concrete Strategy` (걷거나 공격하는 방법)를 바꿀 수 있도록 하였다.

```kotlin
val cpu = CPU()
val ssd = SSD()
val computer = Computer(cpu, ssd)
```

##### 코드

```kotlin
// 1. Strategy Interface
interface MovingStrategy { fun move() }
interface SpeakingStrategy { fun speak() }

// 2. Concrete Strategy
class WalkStrategy: MovingStrategy {
    override fun move() {
        print("WALK")
    }
}

class RunStrategy: MovingStrategy {
    override fun move() {
        print("RUN")
    }
}

class EnglishStrategy : SpeakingStrategy {
    override fun speak() {
        print("abc")
    }
}

class KoeanStrategy: SpeakingStrategy {
    override fun speak() {
        print("가나다")
    }
}

// 3. Abstract context (cannot create an instance)
abstract class Character(private var name: String) {
    var movingStrategy: MovingStrategy? = null
    var speakingStrategy: SpeakingStrategy? = null

    fun move() {
        movingStrategy?.move()
    }

    fun speak() {
        speakingStrategy?.speak()
    }
}

// 4. Concrete context
class TypeA(name: String) : Character(name)
class TypeB(name: String) : Character(name)


@Test
fun testStrategyPattern() {
    val typeA: Character = TypeA("철수")
    typeA.movingStrategy = WalkStrategy()
    typeA.speakingStrategy = EnglishStrategy()

    typeA.move()
    typeA.speak()
}
```
