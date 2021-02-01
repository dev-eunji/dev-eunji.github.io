---
title: "[Android] Service Lifecycle"
categories: [Android]
tags: [android, lifecycle, service, service lifecycle]
last_modified_at: 2021-01-31
comments: true
---

Service 도 Activity와 마찬가지로 lifecycle을 가지며, lifecycle 콜백 메서드가 있습니다.

Service를 실행시킬 수 있는 방법은 2가지로, `startService()` 또는 `bindService()`를 호출하는 방법이 있습니다.
호출되는 방법에 따라 `시작된 서비스`, 그리고 `바인딩된 서비스` 로 구분해서 볼 수 있다.

- 단, 모든 서비스는 클라이언트와 바인딩될 수 있다. 즉, `startService()`로 실행된 서비스라도 `bindService()`가 호출되어 다른 클라이언트에 바인딩 될 수 있다.

<figure>
<img src="/assets/images/android/android_service_lifecycle.jpeg" width="500" height="500" />
<figcaption>[그림] 안드로이드 developer 사이트 참고</figcaption>
</figure>

##### onCreate()

- 서비스가 생성될 때 최초로 호출됨
- 초기 설정을 수행

##### onStartCommand()

- `startService()` 로 Service가 시작될 때 호출됨.
  - `startService()`를 통해 전달한 Intent를 `onStartCommand()` 에서 서비스가 수신하게 된다.
- 서비스는 작업이 완료되면 `stopSelf()`를 호출하여 스스로 중단하거나 다른 컴포넌트가 `stopService() `를 호출하여 중단시킬 수 있다. (콜백 없음)
  - `onStartCommand()`호출을 한 번이라도 받은 서비스는 lifecycle을 직접 관리 및 중단해야 한다. (이후 `onBindCommand()`가 호출 되었더라도)
    - 시스템이 서비스를 중단하거나 소멸시키지 않음

##### onBind()

- `bindService()` 로 클라이언트가 Service에 binding될 때 호출됨.

##### onUnbind()

- **모든** 클라이언트들이 `unbindService()`를 호출해서 binding이 해지되었을 때 호출 됨.

##### onRebind()

- `unbindService()`가 호출된 이후에, 클라이언트가 `bindService()`를 호출하여 Service에 binding될 때 호출됨.

##### 5. onDestory()

- Service가 해지될 때 호출 됨.
- 리소스 해지를 수행

---

**Service 대한 추가 설명**

1. Service는 백그라운드에서 무한히 실행될 수 있는 컴포넌트로 Service를 실행시킨 컴포넌트가 종료 되었더라도 Service는 여전히 살아있을 수 있다.

2. Service는 기본적으로 `main thread`에서 실행되기 때문에 user와의 인터렉션 성능을 저하시킬 수 있습니다.
   따라서, Service 내에 `new thread` 를 생성하여 작업을 수행함으로써 성능저하를 막을 수 있습니다.

3. Service는 항상 **명시적** 인텐트만 사용하자.

- `android:exported=false`로 설정하여, 다른 앱에서 서비스를 호출하지 못하게 하자. (보안)
- `Android 5.0` 부터 암시적 인텐트로 bindService() 호출시, 시스템이 예외를 발생시킴.

---

**JobIntentService**

```java
public abstract class JobIntentService extends Service
```

- `Service`와 차이점

  - 시스템에 의해 언제든지 `onStopCurrentWork()`가 호출되어 중지될 수 있는 것이 차이점이다.
  - `startService()`가 아니라 `enqueueWork()` 호출로 실행됨
  - `JobScheduler` 를 사용하므로 `android.permission.BIND_JOB_SERVICE` permission 필요.
    ```java
    <service
    android:name=“com.test.MyIntentService”
    android:permission=“android.permission.BIND_JOB_SERVICE" />
    ```

- `IntentService`와 차이점
  - `IntentService`는 Android 8 Oreo부터 제대로 작동하지 않으며(백그라운드 실행 제한), Android 11부터 사용불가.
  - `JobIntentService`는 `onHandleWork()`를 통해 설정한다. (IntentService는 `onHandleIntent()`)

---

##### 참고

- https://developer.android.com/guide/components/services
- https://developer.android.com/training/run-background-service/send-request
