---
title: "[Design Pattern] 빌더 패턴 (The Builder Design Pattern)"
categories: [CS]
tags: [CS, UML, 디자인패턴, 빌더패턴, builder, builder pattern]
last_modified_at: 2021-02-06
comments: true
---

## 빌더 패턴 (The Builder Design Pattern)

> 빌더 패턴을 `Effective Java-아이템2` 와 여러 블로그들의 예제를 보고, kotlin 에 적용해보면서 작성한 포스트 입니다.

`객체를 생성할 때 사용하는 패턴` 으로 특히, 매개변수가 많다면 빌더패턴 사용을 고려해보야하 합니다.

빌더 패턴은 `점층적 생성자 패턴`의 안전성과 `자바빈즈`의 높은 가독성의 장점을 취한 패턴이라 할 수 있습니다.
(단, `Builder` 클래스가 함께 필요하기 때문에 점층적 생성자 패턴에 비해 코드의 길이가 길어질 수 있다.)

그러면, 빌더 패턴을 보기 전에 `점층적 생성자 패턴`과 `자바빈즈`에 대해 간단히 우선 알아보자.

#### 1. 점층적 생성자 패턴

`점층적 생성자 패턴`은 선택 매개변수가 0개인 생성자 부터, 모든 선택 매개변수를 받는 생성자까지 점층적으로 늘려가는 방식이다.

많이 사용되는 생성 방법이기는 하지만 매개변수의 개수가 너무 많아지면, 클래스를 호출하는 클라이언트쪽 소스의 가독성이 떨어지며 객체를 생성하는 과정에서 실수할 수 있는 여지가 생기게 됩니다.

##### 코드

````kotlin
class User {
    private lateinit var name: String           // 필수
    private lateinit var phoneNumber: String    // 필수
    private lateinit var address: String        // 선택
    private lateinit var hobby: String          // 선택
    private lateinit var specialty: String      // 선택

    constructor(name: String, phoneNumber: String) : this(name, phoneNumber, "")

    constructor(name: String, phoneNumber: String, address: String) : this(name, phoneNumber, address, "")

    constructor(name: String, phoneNumber: String, address: String, hobby: String) : this(name, phoneNumber, address, hobby, "")

    constructor(name: String, phoneNumber: String, address: String, hobby: String, specialty: String) {
        this.name = name
        this.phoneNumber = phoneNumber
        this.address = address
        this.hobby = hobby
        this.specialty = specialty
    }

    // for test
    override fun toString() = "name: ${name},  phoneNumber: ${phoneNumber}, address: ${address}, hobby: ${hobby}, specialty: ${specialty}"
}


@Test
fun userTest() {
    print("user: ${User("이름", "010-1234-5678", "주소")}")
}
```
java 코드라면, 아래와 같이 생성자를 작성할 수 있을 것입니다.

```java
class User {

    public User(String name, String phoneNumber) {
        this(name, phoneNumber, "")
    }

    public User(String name, String phoneNumber, String address) {
        this(name, phoneNumber, address, "")
    }

}
````

#### 2. 자바빈즈

매개변수가 없는 생성자로 객체를 생성한 후에, `setter` 메서드들을 호출하여 매개변수 값을 설정하는 방법이다.

원하는 매개변수를 모두 갖는 완전한 객체가 생성되기 전까지 일관성이 깨져있는 상태에 있게 되는 단점이 있다.
즉, 자바빈즈 패턴으로는 `불변(immutable)`객체를 생성할 수 없다.

##### 코드

```kotlin
data class User(
    var name: String = "",          // 필수
    var phoneNumber: String = "",   // 필수
    var address: String = "",       // 선택
    var hobby: String = "",         // 선택
    var specialty: String = ""      // 선택
)

@Test
fun userTest() {
    val user = User()
    user.name = "이름"
    user.name = "이름2"
    user.phoneNumber = "123"
    print("$user")
}
```

- 참고: [`val` 은 `immutable`이 아니라 `read-only`](https://xabaras.medium.com/kotlin-val-is-read-only-not-immutable-585ce2e5359b)

#### 3. 빌더 패턴

`effective java` 는 빌더 패턴은 계층적으로 설계된 클래스와 함께 사용하기에 좋은 패턴이라고 설명한다.

##### 코드

```kotlin
class User private constructor(builder: Builder) {
    private val name: String           // 필수
    private val phoneNumber: String    // 필수
    private val address: String        // 선택
    private val hobby: String          // 선택
    private val specialty: String      // 선택

    class Builder(var name: String, var phoneNumber: String) {
        var address = ""
            private set
        var hobby = ""
            private set
        var specialty = ""
            private set

        fun name(name: String) = apply { this.name = name }
        fun phoneNumber(phoneNumber: String) = apply { this.phoneNumber = phoneNumber }
        fun address(address: String) = apply { this.address = address }
        fun hobby(hobby: String) = apply { this.hobby = hobby }
        fun specialty(specialty: String) = apply { this.specialty = specialty }
        fun build(): User = User(this)
    }

    init {
        name = builder.name
        phoneNumber = builder.phoneNumber
        address = builder.address
        hobby = builder.hobby
        specialty = builder.specialty
    }
}

@Test
fun userTest() {
    val user = User.Builder("이름", "010-1234-5678")
        .address("주소")
        .hobby("취미")
        .build()
}
```

참고로, kotlin의 `apply()`를 사용하여 간결하게 Builder 코드를 작성할 수 있다.

```kotlin
// java style.
fun name(name: String): Builder {
    this.name = name
    return this
}

// kotlin style.
fun name(name: String) = apply { this.name = name }
```

더 나아가, `data class` 를 사용해서 아래와 같은 방식으로도 구현할 수 있다.

##### 코드2

```kotlin
class User private constructor(
    val name: String,
    val phoneNumber: String,
    val address: String,
    val hobby: String,
    val specialty: String
) {
    data class Builder(
        var name: String = "",
        var phoneNumber: String = "",
        var address: String = "",
        var hobby: String = "",
        var specialty: String = ""
    ) {
        fun name(name: String) = apply { this.name = name }
        fun phoneNumber(phoneNumber: String) = apply { this.phoneNumber = phoneNumber }
        fun address(address: String) = apply { this.address = address }
        fun hobby(hobby: String) = apply { this.hobby = hobby }
        fun specialty(specialty: String) = apply { this.specialty = specialty }
        fun build() = User(name, phoneNumber, address, hobby, specialty)
    }
}

@Test
fun userTest() {
    val user: User = User.Builder()
        .name("이름")
        .address("주소")
        .hobby("취미")
        .build()

    // val user: User = User.Builder("이름", "주소", "취미").build()
    // val user: User = User.Builder(name = "이름", hobby = "취미", address = "주소").build()
}
```

---

##### 참고

- [https://blog.kotlin-academy.com/effective-java-in-kotlin-item-2-consider-a-builder-when-faced-with-many-constructor-parameters-1927e69608e1](https://blog.kotlin-academy.com/effective-java-in-kotlin-item-2-consider-a-builder-when-faced-with-many-constructor-parameters-1927e69608e1)

- [https://www.baeldung.com/kotlin/builder-pattern](https://www.baeldung.com/kotlin/builder-pattern)
