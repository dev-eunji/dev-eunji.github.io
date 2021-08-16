---
title: "[Github] 토큰인증 로그인: Please use a personal access token instead."
categories: [Etc]
tags: [github, sourcetree, token]
last_modified_at: 2021-08-16
toc: true
comments: true
---

> 2021년 8월 13일에 ID/PW 방식의 로그인 인증방에서 ID/Token 방식으로 로그인 방식이 변경되면서 소스트리에서 소스를 push를 할 때 에러를 만났다.

1. 소스트리로 깃헙에 push 를 하려고 할 때 아래와 같은 오류메시지를 보게 되었다.
   <img src="/assets/images/etc/github-login-token1.png" width="500" height="350" />

2. 토큰을 생성한다.

   - 방법: 깃헙 로그인 > 프로필 클릭 > Settings 클릭 > Developer Settings 클릭 > Personal access tokens 클릭 > Generate new token 클릭

3. ghp\_ 로 시작하는 토큰 값을 얻게되는데, 다시 볼 수 없으므로 꼭 따로 저장해둔다.

4. 다시 소스트리로 돌아와서 push를 시도해도 1.과 동일한 에러메시지를 만나게 되어 기존 로컬에 저장된 계정 정보를 삭제하기 위해 아래와 같이 진행한다.

   - `$ cd /Users/ [사용자 이름] /Library/Application Support/SourceTree`
   - ls 로 폴더 내의 내용들을 확인하고,`@STAuth-github.com` 으로 끝나는 깃헛 계정을 삭제한다.
     `$ rm [아이디]\[@메일]\@STAuth-github.com`

5. 다시 소스토리로 돌아와서 push를 진행한다. 이제 PW 대신에 token 값을 넣어주면 된다 😀
   <img src="/assets/images/etc/github-login-token2.png" width="500" height="350" />
