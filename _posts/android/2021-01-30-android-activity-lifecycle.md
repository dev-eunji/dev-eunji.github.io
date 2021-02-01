---
title: "[Android] Activity Lifecycle"
categories: [Android]
tags: [android, lifecycle, activity, activity lifecycle]
last_modified_at: 2021-01-30
comments: true
---

개발자의 의도대로, 그리고 User가 예상하는대로 앱이 동작되도록 하기 위해서는 Activity lifecycle 에 대한 이해는 필수라고 할 수 있습니다.

Activity lifecycle 변화를 알 수 있는 콜백들이 있으며, system이 해당 콜백 메소드들을 호출합니다.

<figure>
<img src="/assets/images/android/android_activity_lifecycle.jpeg" width="500" height="500" />
<figcaption>[그림] 안드로이드 developer 사이트 참고</figcaption>
</figure>

##### 1. onCreate()

- 액티비티 최초 생성 시 1회 호출

- 반드시 구현이 필요하며, `setContentView()`를 호출하여 layout을 정의함

##### 2. onStart()

- User에게 화면에 보이기 직전에 호출

- 매우 빠르게 완료되며, 바로 `onResume()`을 호출

##### 3. onRestart()

- 중단(onStop())되었다가 다시 시작(onStart()) 되기 직전에 호출

##### 4. onResume()

- User에게 화면이 보여질 때 호출

- User와 상호작용할 수 있는 상태이며, focus가 떠날 때 까지 이상태에 머물게 된다.

##### 5. onPause()

- Activity 가 User에게 **완전히** 보이지 않게 되기 직전까지 이 상태에 머물게 된다.
- `onPause()`는 아주 잠깐 실행되는 콜백 함수이므로 시간이 오래 걸리는 작업을 하면 **안된다.** (network, DB 작업 등)

  - 부하가 큰 종료 작업은 `onStop()`에서 수행하자.

- 멀티윈도우 모드가 실행될 때, 불투명 Activity 가 최상단에 띄워질 때 (화면에는 일부 보이지만, focus 상태는 아닌 경우) 등의 경우에 호출될 수 있다.

  - `onPause()` 에서는 화면이 보일 수 있는 상태이므로, UI 리소스 해지 작업은 `onStop()`에서 수행하자.

##### 6. onStop()

- 액티비티가 User에게 완전히 보여지지 않을 때 호출

- 다른 Activity가 포그라운드 전체를 차지하거나 앱이 백그라운드로 내려가서 Activity가 더이상 User에게 보이지 않을 때 호출된다.

  - 백그라운드로 내려갔지만 focus 만 잃은 상태일 경우엔 `Paused`.

- CPU를 비교적 많이 소모하는 종료 작업(network, DB 작업 등), UI 해지 작업(애니메이션 일시중지 등)을 `onStop()`에서 실행하자.

- `onRestart()` 또는 `onDestroy()`를 호출

##### 7. onDetrosy()

- Activiy가 소멸되기 전에 호출

- finish() 호출, Configuration 변경(기기회전,, 멀티윈도우 모드)

- ` isFinishing()` onPause(), onDestroy()에서 액티비티가 단순 중지 또는 구성이 변경 된건지 진짜 종료(finish)된건지 확인할 수 있다.

---

Activity 의 생명주기에 따른 로직을 아래와 같이 Activity 콜백에 직접 구현할 수도 있지만,

```
class MainActivity : AppCompatActivity() {
    // ...
    override fun onResume() { }
}
```

**수명주기 인식 컴포넌트** 를 별도로 구현하면, 여러 Activity에서 재사용이 가능한 장점이 있다. 따라서, 앱 전반에 재사용이 필요한 경우라면 수명주기 인식 컴포넌트 사용을 고려해보세요.

```
class MyLifecycleObserver : LifecycleObserver {
    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    fun onResume() { }
}
```

- ON_START 이후에 초기화 -> ON_STOP 이후에 해제
  ON_RESUME 이후에 초기화 -> ON_PAUSE 이후에 해제

- 참고: https://developer.android.com/guide/components/activities/activity-lifecycle
