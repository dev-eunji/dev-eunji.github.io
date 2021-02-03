---
title: "[Android] View lifecycle"
categories: [Android]
tags: [android, lifecycle, view, view lifecycle]
last_modified_at: 2021-02-03
comments: true
---

## View는 어떻게 그려지나?

> [안드로이드 개발자 페이지 how-android-draws](https://developer.android.com/guide/topics/ui/how-android-draws) 를 기반으로 작성한 포스트입니다.

View의 lifecycle을 살펴보기에 앞서, 우선 View가 어떻게 그려지는지를 먼저 살펴보면 좋을 것 같습니다.

Activity는 focus 를 받으면 OS(`Android`)에게 view hierarchy의 루트노드를 제공하여 layout을 그리게 된다. (`onCreate()`에서 `setContentView()`을 통해 루트노드를 전달)

layout이 그려지는 과정은 2 단계로, 각각 **measure**와 **layout** 단계이다.

layout은 루트노드부터 그리며, `layout tree` 를 따라 부모가 먼저 그려지고 그다음 자식들 순서대로(`top-down`)그려진다.

- `부모view(ViewGroup)`는 `자식view들`의 `draw()`를 호출하여 view 그리기를 요청한다.
- 모든 각각의 view들은 스스로 그려질 책임이 있다.
  (뒤의 이어지는 글들을 통해 이 문장의 의미를 자세히 알 수 있다.)

#### 1. measure 단계

- `measure()`. 측정이 완료되면, 모든 view들은 각각 자신의 측정 값을 저장한다.

- View의 `measure()`가 반환되면, `getMeasuredWidth()` 및 `getMeasuredHeight()` 값을 `후손(descendants) view`들의 값과 함께 설정해야한다.
- `부모view`는 자식들에게 두번 이상의 `measure()`를 호출하게 될 수도 있다.(`UNSPECIFIED` 로 한번 측정해보고, `자식view들`의 크기 합이 너무 크거나 작을 때)

- measure() 단계에서 사용되는 2가지 class

A. ViewGrop.LayoutParams
: `자식view`가 `부모view`에게 본인이 어떻게 측정되고 싶은지를 알릴 때 사용한다.

    - 정확한 숫자
      `android:layout_width="40dp"`

    - MATCH_PARENT
      `android:layout_width="match_parent"`

    - WRAP_CONTENT
      `android:layout_width="wrapcontent"`

B. ViewGroup.MeasureSpecs
: `부모view`가 `자식view`의 크기 제한을 위해 사용한다.

    - UNSPECIFIED: 제한 없음. (`자식view`의 `measure()`호출)
    - EXACTLY: `자식view`의 정확한 사이즈를 설정한다. `자식view`는 해당 사이즈 내에서 `후손view들`을 맞춰야한다.
    - AT_MOST: 자식view의 최대 사이즈를 설정한다.

#### 2. layout 단계

- `layout()`. `부모view`는 `measure단계`에서 측정된 크기를 사용하여 모든 `자식 view들`의 위치를 배정한다.

---

## View Lifecycle

> 안드로이드 개발자 페이지에서는 직접적인 view의 lifecycle에 대한 글을 찾기 어려워서 [안드로이드 개발자 페이지 View](https://developer.android.com/reference/android/view/View.html)와 여러 개발블로그들을 통해 공부한 내용을 작성한 포스트입니다.

View 역시 lifecycle을 가지며 custom view를 만들거나, layout이 어떻게 그려지는지를 이해하기 위해서는 view의 생명주기를 이해하는 것이 중요할 것이라 생각된다.

<figure>
<img src="/assets/images/android/android_view_lifecycle.jpeg" width="500" height="500" />
<figcaption>[그림] 개발블로그 참고</figcaption>
</figure>

##### constructor()

- 모든 뷰는 생성자에서 출발 (`AttributeSet`)
- `addView()`

##### 1. onAttachedToWindow()

- View가 Window에 붙을 때 호출된다.

  - `부모view(ViewGroup)` 가 `addView()` 를 호출

- View에 `id`로 접근이 가능해짐.

- 그리기를 위한 `surface`를 갖는다.
  - `onDetachedFromWindow()`호출 후에는 그리기를 위한 `surface`가 없다. (Activity destroyed, `부모view`에서 remove될 때 호출됨.)

##### 2. onMeasure()

- `measure()` 에서 호출하는 콜백으로, View의 크기를 측정한다.

  - `부모view(ViewGroup)`의 경우, 모든 `자식view`들의 `measure()` 를 호출하여 크기를 결정한다.

- `MeasureSpec`

##### 3. onLayout()

- `layout()` 에서 호출하는 콜백으로, iew의 크기와 위치를 지정한다.

##### dispatchDraw()

- `ViewGroup`에 속한 메소드 (`ViewGroup.dispatchDraw()`)로, `자식 View들`도 다시 그려진다.

##### 4. onDraw()

- 실제 View를 그리는 단계

  - `Canvas`로 모양을 `Paint`로 색을 그린다.

- 여러번 호출될 수 있는 콜백으로 onDraw() 내에서 객체 생성을 하면 안된다.
  - `scroll`, `swipe` 등의 인터렉션이 들어오면 또 호출될 수 있다.

---

### View 업데이트

##### invalidate()

- 언제 호출하먄 될까?
  : txt, color 등일 변경될 때 등 크기의 변화는 없이 단순 View를 다시 그려야할 때 또는 강제로 view를 그리고자 할 때

##### requestLayout()

- 언제 호출하먄 될까?
  : 뷰의 크기가 변경 될 때

---

- 참고: https://proandroiddev.com/android-draw-a-custom-view-ef79fe2ff54b
