---
title:  "[Blog] 로컬 PC에서 jekyll 확인하는 방법"
categories: [Etc]
tags: [Blog, jekyll, local]
last_modified_at: 2020-11-23
toc: false
comments: true
---

> jekyll 블로그의 코드를 변경하여 UI를 수정하였거나, 새로운 글을 작성하고 업로드 하기 전에 실제 어떻게 보여질지 궁금한데

간단하게 아래와 같은 방법으로 git 에 배포하기 전에 내 컴퓨터에서 확인해볼 수 있다.

1. Terminal 을 켠다.
2. `$ cd` 명령어로 jekyll 블로그가 있는 폴더 위치까지 이동한다.
3.  `$ bundle exec jekyll serve`

![Jekyll_Run_Local](/assets/images/etc/how-to-run-jekyll-local.png)

4. 크롬을 켜고, `localhost:4000` 을 입력한다.

5. (변경 사항이 확인이 안된다면, 조금 기다린 후에 다시 refresh 해본다.)