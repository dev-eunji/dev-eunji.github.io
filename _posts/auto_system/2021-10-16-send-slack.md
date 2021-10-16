---
title: "[Python] 파이썬 코드로 Slack 전송하기"
categories: [Auto System]
tags: [python, slack]
last_modified_at: 2021-10-16
comments: true
---

# Intro.

자동 프로그램을 돌릴 때, 특정 상황에서 메시지를 받고 싶은 경우가 있다.
메시지를 내 Slack 채널로 전송하는 방법과 짧은 코드를 알아보자.

# 방법

### 1. Slack 워크스페이스 및 채널 생성

##### A. [slack]() 홈페이지 > 새 워크스페이스 생성 버튼 클릭 > 워크스페이스 생성

<img src="/assets/images/auto_system/send_slack1.png"  width="500"/>

##### B. 워크스페이스 생성

- 워크스페이스 버튼 클릭
  <img src="/assets/images/auto_system/send_slack2.png"  width="500"/>

- 1단계: 워크스페이스 이름 입력
  <img src="/assets/images/auto_system/send_slack3.png" width="450" />

- 2단계: 간단한 설명 작성
- 3단계: 건너뛰기

### 2. 봇 역할을 수행할 앱 생성 및 궈한 부여

##### A. [slack API](https://api.slack.com) 홈페이지 > 'Create an app' 버튼 클릭 > 'Create new app' 버튼 클릭

  <img src="/assets/images/auto_system/send_slack7.png"  width="500"/>

- `From scratch` 선택한 후, `앱 이름(봇의 이름)`을 입력하고 `워크스페이스`를 선택한다.
  <img src="/assets/images/auto_system/send_slack8.png" width="450" />
  <img src="/assets/images/auto_system/send_slack9.png" width="450" />

##### B. 사이드 메뉴 > Settings/OAuth & Permissions 메뉴

- 아래로 스크롤하여 `Scopes` 를 지정해준다. (`Bot Token Scops`)
  - `chat:write`와 `chat:write.public` 권한을 부여한다.
  - 각각 메시지 보내기 권한과 멤버가 아닌 채널로 메시지 보내기 권한이다.
    <img src="/assets/images/auto_system/send_slack11.png" width="450" />
    <img src="/assets/images/auto_system/send_slack12.png" width="450" />

### 3. 테스트

##### A. `사이드 메뉴 > Settings/OAuth & Permissions` 에서 확인 가능한 `Bot User OAuth Token`을 복사한다.

- 코드의 token_bot 변수에 붙여넣기 한다.

##### B. 아래 코드를 프로그램에 작성한 후 실행 > 채널에 메시지가 들어온 것을 확인한다.

<img src="/assets/images/auto_system/send_slack15.png" width="500" />

# 코드

```python
import requests
import datetime

token_bot = "xoxb-..."
channel_name = "#..."

def send_slack(token, channel, text):
    response = requests.post("https://slack.com/api/chat.postMessage",
                             headers={"Authorization": "Bearer " + token},
                             data={"channel": channel, "text": text}) # <Response [200]>

message = datetime.datetime.now().strftime('[%m/%d %H:%M:%S] ') + "HELLO WORLD!"
send_slack(token_bot, channel_name, message)
```

# 참고

- [slack API](https://api.slack.com)
- [조코딩의블로그](https://developerdk.tistory.com/96)
- [INVESTAR/StockAnalysisInPython](https://github.com/INVESTAR/StockAnalysisInPython/blob/master/08_Volatility_Breakout/ch08_03_EtfAlgoTrader.py)
