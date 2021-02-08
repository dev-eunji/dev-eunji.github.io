---
title: "[Android] 안드로이드에 FCM으로 Push 서비스 빠르게 적용해보기"
categories: [Android]
tags: [android, FCM, Firebase]
last_modified_at: 2021-02-08
comments: true
---

## [FCM](https://firebase.google.com/docs/cloud-messaging/android/client) 이란?

`FCM`은 `Firebase Cloud Message` 의 약자로, 알림 메시지를 전송할 수 있는 교차 플랫폼 메시징 서비스이다.

## FCM 빠르게 앱에 적용해보기

### 0. 들어가기 전에

Android Studio를 최신버전으로 업데이트 해주세요. (권장사항)

### 1. Firebase 프로젝트 생성

1. [Firebase Console](https://console.firebase.google.com/) 로 이동
2. 프로젝트 추가 버튼 클릭 > 프로젝트 이름 입력 > Google Analytics 설정여부 세팅
   <img src="/assets/images/android/fcm/fcm_create_new_project.png" width="300" height="300" />

### 2. 앱 등록 및 사용 설정

1. 생성된 프로젝트 메인 화면 > 안드로이드 아이콘 클릭
   <br><img src="/assets/images/android/fcm/fcm_add_app.png" width="300" height="300" />

2. 앱 패키지명 입력 > 앱 등록 버튼 클릭

3. `google-services.json` 다운로드 후, 앱 폴더 > app 폴더 내로 옮깁니다.

4. 생성된 프로젝트 메인 화면 > 사이드 > 톱니바퀴 모양 아이콘 클릭 > 프로젝트 설정 클릭

   <img src="/assets/images/android/fcm/fcm_project_setting.png" width="300" height="300" />

5. `build.gradle` 설정 및 sync.

- 프로젝트 수준

  ```gradle
  buildscript {
  repositories {
    google()
  }
  dependencies {
    classpath 'com.google.gms:google-services:4.3.5'
  }

  allprojects {
    repositories {
      google()
    }
  }
  ```

- app 수준

  ```gradle
  plugins {
    id 'com.android.application'
    id 'com.google.gms.google-services'
  }
  dependencies {
  implementation platform('com.google.firebase:firebase-bom:26.4.0')
  implementation 'com.google.firebase:firebase-analytics-ktx'
  implementation 'com.google.firebase:firebase-messaging'

  // 위의 BoM 라이브러리를 사용하지 않는다면, 아래와 같이 버전을 지정해야합니다.
  // implementation 'com.google.firebase:firebase-messaging:21.0.1'
  }
  ```

### 4. 테스트 앱 구현

1. `FirebaseMessagingService` 상속받는 클래스 추가

   ```kotlin
   class MyFirebaseMessagingService : FirebaseMessagingService() {
    override fun onNewToken(token: String) {
        Log.d(TAG, "onNewToken() token: $token")
    }

    override fun onMessageReceived(remoteMessage: RemoteMessage) {
        Log.d(TAG, "onMessageReceived() remoteMessage: ${remoteMessage}")
    }
   }
   ```

2. `AndroidMnifest.xml` 설정

   ```xml
   <uses-permission android:name="android.permission.INTERNET" />

   <service
   android:name=".MyFirebaseMessagingService"
   android:exported="false">
      <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
      </intent-filter>
   </service>
   ```

### 5. 동작 테스트

1. Firebase console > 프로젝트 메인화면 > 참여 > Cloud Messaging 클릭

2. `Send your first message` 버튼 클릭

3. title 과 description 입력 후 전송
   <br><img src="/assets/images/android/fcm/fcm_new_alarm.png" width="300" height="300" />

4. 로그 확인
