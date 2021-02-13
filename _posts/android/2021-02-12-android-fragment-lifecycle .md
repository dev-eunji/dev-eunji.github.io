---
title: "[Android] Fragment Lifecycle"
categories: [Android]
tags: [android, lifecycle, fragment, fragment lifecycle]
last_modified_at: 2021-02-13
comments: true
---

> [안드로이드 개발자 사이트 Fragment Lifecycle](https://developer.android.com/guide/fragments/lifecycle) 을 공부하고 정리한 내용입니다.

<figure>
<img src="/assets/images/android/android_fragment_lifecycle.jpeg" width="500" height="500" />
<figcaption>[그림] 안드로이드 developer 사이트 참고</figcaption>
</figure>

---

Fragment 에도 lifecycle이 있으며, Fragment의 View에도 별도의 lifecycle기 때문에 함께 알아보고자 합니다.

## 개요

##### Fragment lifecycle

A. Fragment는 lifecycle 관리를 위해 LifecycleOwner 를 implements 하고 있다.

```kotlin
package androidx.fragment.app;

public class Fragment implements LifecycleOwner,
```

- Lifecycle state는 Lifecycle.State enum으로 표시된다.

  - `INITIALIZED`, `CREATED`, `STARTED`, `RESUMED`, `DESTROYED`

  ```kotlin
  Log.d(TAG, "${lifecycle.currentState.toString()}")
  ```

<br>
B. fragment 클래스에는 lifecycle 변경에 해당하는 콜백 메서드가 있다.

- `onCreate()`, `onStart()`, `onResume()`, `onPause()`, `onStop()`, `onDestroy()`

#### Fragment View lifecycle

`getViewLifecycleOwner()`와 `getViewLifecycleOwnerLiveData()` 로 View lifecycle에 접근할 수 있다.

- Fragment의 View가 존재햐는 동안에만 작업을 수행해야하는 상황에 유용하게 활용될 수 있을 것

```kotlin
Log.d(TAG, "${viewLifecycleOwner.lifecycle.currentState.toString()}")
```

## Fragment와 FragmentManager

Fragment가 인스턴스화되면 **INITIALIZED** 상태에서 시작하며, 다른 수명주기로 전환하려면 FragmentManager에 추가 되어야한다.

**FragmentManager는 무엇일까?**

1. Fragment lifecycle에 관여하며
   - Fragment가 있어야하는 상태를 결정하고 해당 상태로 이동시킴
2. Fragment를 host activity에 연결/분리시키는 역할을 한다.
   - Fragment는 onAttach(), onDetach() 콜백 메서드를 갖는다.

**1. onAttach()**

- `FragmentManager`에 Fragment가 추가되고, host activity 에 연결될 때 호출됨.
- `FragmentManager`의 `findFragmentById()`가 해당 Fragment를 반환함.
- lifecycle state가 변하기 전에 호출됨

**2. onDetach()**

- `FragmentManager`에서 Fragment가 제거되고, host activity 에서 분리될 때 호출됨.
- `findFragmentById()`를 사용하여 Fragment를 검색할 수 없다.
- Lifecycle state 변경 된후에 호출됨.

## Fragment와 Fragment View의 lifecycle

#### 1. Fragment CREATED

```
Fragment CREATED, onCreate()
```

- CREATED 상태에 도달하면, `FragmentManager`에 `Fragment`가 추가되고 `onAttach()` 가 이미 호출 된 것.

- `Fragment의 View`는 아직 생성되지 않은 상태이다.

  ```kotlin
  /**
  * savedInstanceState fragment가 처음 생성 될 때는 null 값을 갖지만,
  * 이후 재생성시에는 onSaveInstanceState()를 재정의하지 않더라도 항상 NOT NULL
  */
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
  }
  ```

#### 2. Fragment CREATED & View INITIALIZED

```
Fragment CREATED, onCreateView()/onViewCreated(), View INITIALIZED
```

- View lifecycle은 Fragment가 유효한 View를 제공하는 경우에만 생성됨

- onCreateView()를 재정의하여 fragment의 view를 inflate 또는 생성 할 수 있다.

- Fragment의 View가 null이 아닌 경우에 `getView()`로 찾을 수 있다.

- View의 초기 상태를 설정하기 적절한 위치.
  - Fragment View의 `RecyclerView` 또는 `ViewPager2` 인스턴스 어댑터를 설정

#### 3. Fragment & View CREATED

```
Fragment CREATED, onViewStateRestored(), View CREATED
```

- `View`가 생성 된 후에 수명주기가 CREATED 상태로 이동됩니다.

  - 이전 `View` 상태가 있는 경우 복원됩니다.

#### 4. Fragment & View STARTED

```
Fragment STARTED , onStart(), View STARTED
```

- `Lifecycle-aware components` 를 Fragment의 `STARTED` 상태에 연결하는 것을 추천!

  - 왜냐하면, 이 상태는 Fragment의 View가 사용 가능함을 보장하며(생성된 경우),
    Fragment의 자식 `FragmentManager`가 `FragmentTransaction`을 수행하는 것이 안전함을 보장함.

#### 5. Fragment & View RESUMED

```
Fragment RESUMED , onResume(), View RESUMED
```

- 이제 사용자가 Fragment와 `인터렉션`할 수 있다!

- Fragment가 visible 되면, 모든 Animator 와 Transition effects(전환 효과)가 완료되었으며
  Fragment가 유저 interaction을 위한 준비가 되었습니다.

- 참고) RESUMED 상태가 아닌 Fragment는 View에 수동으로 focus 또는 visibility 등을 설정하려고 시도하면 안됨.

## Fragment and View STARTED

```
Fragment STARTED, onPause(), View STARTED
```

- 사용자가 Fragment에서 나가기 시작했지만, Fragment는 여전히 `visible` 일때

## Fragment and View CREATED

```
Fragment CREATED, onStop()/onSaveInstacneState(), View CREATED
```

- Fragment가 더 이상 `visible` 이 아닐 때

- 부모 Fragment(Activity)가 중지 될 때 또는 부모에 의한 상태 저장에 의해 트리거 됩니다.

- API 에 따라 `onStop()`, `onsavedInstaceState()`의 호출 순서가 다르지만,
  Fragment의 상태가 저장되기 전에 `ON_STOP` 이벤트가 호출되는 것은 보장함.
  (즉, `ON_STOP` 이벤트는 자식 `FragmentManager`에서 `FragmentTransaction`을 안전하게 수행할 수 있는 마지막 지점)
  - API 28 미만: ON_STOP -> onsavedInstaceState() -> onStop()
  - API 28 이상: ON_STOP -> onStop() -> onsavedInstaceState()

## Fragment CREATED and View DESTROYED

```
Fragment CREATED, onDestroyView(), View DESTROYED
```

- Fragment의 View는 window에서 분리되면(`detached`)
- `getViewLifecycleOwnerLiveData()`는 null 반환
- 모든 View의 참조를 제거해야 Garbage 콜렉트될 수 있다.
- `View` lifecycle은 `DESTROYED` 상태로 이동 >
  관찰자에게 ON_DESTROY 이벤트를 보냄 > onDestroyView() 콜백을 호출합니다.

## Fragment DESTROYED

```
Fragment DESTROYED, onDestroy()
```

- Fragment 또는 `FragmentManager`가 제거될 때
