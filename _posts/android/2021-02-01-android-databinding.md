---
title: "[Android] DataBinding"
categories: [Android]
tags: [android, dataBinding, 데이터바인딩]
last_modified_at: 2021-02-02
comments: true
---

> [안드로이드 개발자 사이트 Data Binding Library](https://developer.android.com/topic/libraries/data-binding) 를 공부하고 정리한 내용입니다.

## DataBinding이란?

Data Binding 라이브러리는 프로그래밍 방식이 아닌 **선언적 (declarative)** 포맷을 사용하여 layout UI 컴포넌트들을 데이터에 바인딩 할 수 있도록 지원 라이브러리입니다.

- 기존 .kt 에서 View에 값을 직접 세팅하는 방식이었다면, Data Binding 활용하여 .xml 에서 직접 View에 데이터를 바인딩할 수 있다.

```kotlin
// asis
tvName.text = "name"
```

```xml
<!-- tobe -->
<TextView
  android:text="@{user.name}" />
```

## 사용법

##### STEP 1. gradle 설정

```gradle
dataBinding {
    enabled = true
}
```

##### STEP 2. Model 클래스 생성

```kotlin
package com.example

data class User(var name = "", var profileUrl = "")
```

##### step3. xml 수정

```xml
<layout>
  <data>
    <variable name ="user" type="com.example.User" />
    <variable name="viewmodel" type="com.example.MainViewModel" />
  </data>

  <LinearLayout>
    <TextView
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="@{user.name}" />
  </LinearLayout>

</layout>
```

- 루트 태그는 `<layout>` 이며, `<layout>` 태그는 자식뷰를 하나만 가질 수 있다. (`NestedScrollView` 처럼)
- `<data>` 내 `<variable>` 에 선언한 객체 변수에 값을 직접 세팅할 수 있다.
  - 기존에는 `findViewById`로 뷰바인딩 후 데이터를 세팅
- `"@{ }"` 안에 바인딩 할 데이터를 작성한다.

##### step4. 데이터바인딩 클래스

```kotlin
class MainActivity : AppCompatActivity() {
  lateinit var databinding: ActivityMainBinding

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    // asis: setContentView(R.layout.activity_main)
    databinding = DataBindingUtil.setContentView(this, R.layout.activity_main)
  }
}
```

- 데이터바인딩 클래스는 컴파일러가 생성해준다.

  - 예를 들어, activity_main.xml 의 데이터바인딩 클래스는 ActivityMainDataBinding.kt

---

**바인딩 어댑터 (Custom Binding Adapter)**

- 객체에 한번에 세팅하고 싶을 때
- 메소드 파라미터를 알아서 찾아줌

```kotlin
object CustomBinding {

  @BindingAdapter("image_url","image_error")
  @JvmStatic fun loadImage(view : ImageView, profileUrl : String, error : Drawable){
    Glide.with(view.context)
    .load(url)
    .error(error)
    .into(imageView)
  }

}
```

```xml
<ImageView
  app:image_url="@{user.profileUrl}"
  app:image_error="@drawable/error_imaage"/>
```

**리스너 바인딩**

```kotlin
class MyViewModel: ViewModel() {
  fun onSaveClick(user: User) {
    // ..
  }
}
```

```xml
<ImageView
  android:onClick="@{()->viewModel.onSaveClick(user)}"/>
```
