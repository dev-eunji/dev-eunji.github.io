---
title:  "[RxJava3] Observable"

categories:
  - android
tags:
  - android
  - rxjava
  - rxjava3
  - observable
  
last_modified_at: 2020-03-21
toc: true # table of contents 목차 보여줌.
---

RxJava 의 핵심 중 핵심이라할 수 있는 클래스이자 

데이터 흐름에서 데이터 발행을 담당하는 Observable에 대해서 알아보겠습니다.

---

Observable은 **3가지 event**를 가집니다.

-   onNext          : 데이터 발행을 알림
-   onComplete  : 데이터 발행 완료를 알림
-   onError         : 에러 발생을 알림
    -   onError 발생 이후에는 onNext 와 onComplete 가 발생하지 않음.



그러면, Observable은 어떻게 생성할 수 있을까요?

just(), fromXX(), create(), interval(), timer(), range() 와 같은 **정적 팩토리 함수**를 활용하여 생성할 수 있습니다.

**Observable의 종류**는 총 4가지가 있습니다.

-   **Observable**: 기본
-   **Maybe**: 0개 또는 1개의 데이터 발행 (Null 가능성 있음)
-   **Flowable**: Back pressure (데이터의 발행속도가 처리하는 속도보다 클 때) 대응 가능



여기에 추가로, 데이터를 1개만 발행하는 **Single** 이 있으며,  onSuccess / onError의 2가지 event만을 갖습니다.



주의할 점은 Observer에서 **subscribe()** 를 호출한 이후에 실제 데이터 발행 발행이 된다는 것입니다.



subscribe()는 Disposable 객체를 반환하는데, onComplete 이벤트 발생 후 **Disposable** 의 dispose() 가 호출되어 Observable과 Observer의 관계가 끊어지게 됩니다.

```
import io.reactivex.rxjava3.core.Observable

@Test
fun testObservableJust() {
    var disposable = Observable.just(1, "A", true) // null 포함되면, NullException 발생
        .subscribe {
            println(it)    // 1 "A" true
        }
    println("disposable: ${disposable.isDisposed}")  // true
}
```

이때, subscribe()를 호출하지 않은 상태의 Observable을 차가운 Observable / 반대는 **뜨거운 Observable ** 입니다.



1) 차가운 **Observable**은 Observer가 subscribe()를 호출하면 (구독 시작!) 준비되어있는 데이터를 처음부터 발행하며, 대부분의 경우 차가운 Observable을 사용.

2) **뜨거운 Observable**은 Obserber가 구독을 시작한 시점부터의 데이터를 발행을 지속적으로 받게됩니다.

따라서, 키보드/마우스 입력, 시스템 이벤트 등 계속 구독이 필요한 때에 사용하면 됩니다.

---

차가운 Observable을 뜨겁게 변활 할 수 있는데, 이때 사용하는 방법이 아래와 같습니다.

-   **Subject** 활용
-   **ConnectableObservable** 활용

두 방법 모두 subscribe와 상관없이 데이터는 발행된다는 특징이 있습니다.

그러면 우선, **Subject** 는 또 뭘까요..?

```
package io.reactivex.rxjava3.subjects;

public abstract class Subject<T> extends Observable<T> implements Observer<T>
```

코드에서 확인해볼 수 있듯이 Observable과 Observer의 특성을 모두 가지고 있는 클래스입니다.

즉, 데이터 발행과 처리, 구독을 모두 할 수 있는 클래스인 것입니다.

**Subject의 종류** 는 대표적으로 PushblishSubject, AsyncSubject, BehaviorSubject가 있습니다. 하나씩 알아봅시다.

-   **PublishSubject**  : 가장 특징이 없는 클래스로 자주 사용이 되며, subscribe() 이후의 데이터들을 받습니다.

아래 마블 다이어그램으로 PublishSubject를 이해해봅시다. 











참고로, 이런 그림들을**마블 다이어그램**은  "비동기" 그리고 "데이터 흐름"과 밀접한Rx 개념을 확인하는 방법이 매우 유용한 방법입니다.

(이 외에 다양한 마블 다이어그램은[http://reactivex.io/documentation/subject.html](http://reactivex.io/documentation/subject.html) 등에서 확인 가능합니다.)



아래와 같이 코드 작성 후 실행해보면, 일반적인 Hot Observable과 동일하게 동작함을 알 수 있습니다. 

```
 val subject = PublishSubject.create<Int>()
    subject.subscribe {
        println("#1 value: $it")
    }
    subject.onNext(1)   // onNext 호출 시, 등록되어있는 옵서버들에게 값을 날림.

    subject.subscribe {
        println("#2 value: $it")
    }
    subject.onNext(2)
    subject.onNext(3)
```

<table style="border-collapse: collapse; height: 97px;" border="1"><tbody><tr><td>#1&nbsp;value:&nbsp;1<br>#1&nbsp;value:&nbsp;2<br>#2&nbsp;value:&nbsp;2<br>#1&nbsp;value:&nbsp;3<br>#2&nbsp;value:&nbsp;3</td></tr></tbody></table>



-   **AsyncSubject**     : Observer가 언제 구독을 했던지, onComplete 직전 마지막 데이터를 동시에 받습니다.

위의 PublishSubject를 AsyncSubject로 변경 후 실행해보면, 구독자 #1, #2 모두 onCompolete 직전 마지막 데이터인 3을 출력하는 것을 확인할 수 있습니다.

```
val subject = AsyncSubject.create<Int>()

subject.subscribe {
    println("#1 value: $it")
}
subject.onNext(1)

subject.subscribe {
    println("#2 value: $it")
}
subject.onNext(2)
subject.onNext(3)
subject.onComplete() // 호출하지 않으면, 아무일도 일어나지 않음.
```

<table style="border-collapse: collapse; width: 100%;" border="1"><tbody><tr><td style="width: 100%;">#1 value: 3<br>#2 value: 3</td></tr></tbody></table>



-   **BehaviorSubject** : subscribe() 직전 가장 최근값 (없다면, default로 지정해놓은 값)  + subscribe() 이후 데이터 들을 받습니다.

#2가 구독하기전에 발행된 데이터 1 도 #2가 구독을 한 것을 확인할 수 있습니다.  즉, BehaviorSubject로 구독시기가발행시기보다늦을 때,직전값을모르는 문제를해결할수있을 것입니다.

```
val subject = BehaviorSubject.create<Int>()

subject.subscribe {
    println("#1 value: $it"
}
subject.onNext(1)

subject.subscribe {
    println("#2 value: $it")
}
subject.onNext(2)
subject.onNext(3)
```

<table style="border-collapse: collapse; height: 113px;" border="1"><tbody><tr><td style="width: 100%;">#1&nbsp;value:&nbsp;1<br><u>#2&nbsp;value:&nbsp;1</u><br>#1&nbsp;value:&nbsp;2<br>#2&nbsp;value:&nbsp;2<br>#1&nbsp;value:&nbsp;3<br>#2&nbsp;value:&nbsp;3</td></tr></tbody></table>



그럼 이제, **ConnectableObservable**은 또 뭘까요..?

데이터 발행을 유예할 수 있으며, 차가운 Observable에서 publish() 를 호출하면 생성됩니다.

ConnectableObservable에는 **connect()** 라는 함수가 등장을 하는데,  connect() 가 호출된 시점부터 ConnectableObservable에 subcribe()를 호출한 적이 있는 Observer들에게 데이터 발행을합니다.

```
var count = 0
        
val observable = Observable
    .range(0, 3)
    .doOnNext { value ->
        println("value: $value, count: $count")
        count++
    }
    .publish()
//  .publish().refCount() // PublishSubject 처럼 동작

observable.connect() // 이 시점에 이벤트 발행
```

---



지금까지 Rx에 핵심이라 할 수 있는 데이터 발행을 담당하는 Observable에 대해 알아 보았는데요.

다음 포스팅에서는 발행된 데이터를 처리할 수 있는 여러 연산자(Operator) 들에 대해서 알아보겠습니다.



---

Android Studio에서 각 클래스나 메소드들에 커서를 두고 **cmd + b** 를 눌러 라이브러리 내에 구현된 코드들을 보면 도움이 될 것 같아서 혹시 필요하신 분들을 위해 방법을 공유합니다. 예를 들면, 아래와 같이 파악하면서 공부할 수 있습니다.

```
// Observable은 ObservableSource를 구현하는구나. (ObservableSource에 커서를 두고 cmd + b)
public abstract class Observable<@NonNull T> implements ObservableSource<T>


// ObservableSource 인터페이스는 subscribe 함수를 가지네.
public interface ObservableSource<@NonNull T> {
    void subscribe(@NonNull Observer<? super T> observer);
}

// subscribe 의 파라미터인 Observer 인터페이스는 
// Observer에게 Observable의 상태를 알리는 이벤트가 정의되어 있구나.
public interface Observer<@NonNull T> {
    void onSubscribe(@NonNull Disposable d);
    void onNext(@NonNull T t);
    void onError(@NonNull Throwable e);
    void onComplete();
}


```