---
title: "[SourceTree] sourcetree remote: Invalid username or password."
categories: [Etc]
tags: [SourceTree, error]
last_modified_at: 2020-11-23
toc: false
comments: true
---

Github password를 바꿨을때 등 종종 만나게 되는 에러메시지인데, 간단한 방법으로 해결 가능하다.

1. Source Tree > Preferences... 메뉴로 이동
![SourceTree_Preferences](/assets/images/etc/sourcetree-preferences.png)

2. Advanced 버튼 클릭
3. Host name과 Username 확인 후, Rmove 버튼 클릭하여 default User 제거

4. 다시 Source Tree로 돌아와 작업 내용들을 `Push` 
![SourceTree_Authentication](/assets/images/etc/sourcetree-authentication.png)

5. Useranme (github 이메일 계정) 과 Password 입력하면 끝!