---
title: "[Blog] jeklly 깃헙 블로그에 Disqus 댓글 연동하기"
categories: [Etc]
tags: [Blog, jekyll, disqus, 댓글]
last_modified_at: 2020-11-28
comments: true
---

## 가입 및 연동 준비

##### 1. [Disqus](https://disqus.com/) > Get Started (가입) 

##### 2. `I want to install Disqus on my site` 선택
* 내 사이트에 댓글 기능을 추가하겠다는 선택지이다.
<center> <img src="/assets/images/etc/disqus_choose.png" width="240" height="500" /> </center>

##### 3. `Website Name` 과 카테고리를 선택 
* Website Name은 뒤에 나올 shortname에 활용되며, 블로그 url 주소를 넣지 않아도 된다.
    <center> <img src="/assets/images/etc/disqus_create_new_site.png" width="500" height="320" /> </center>

##### 4. 요금제 선택 및 플랫폼 선택
* `Basic`(무료) 선택 후, 필요시 업그레이드를 시키는 것을 추천
    <center> <img src="/assets/images/etc/disqus_choose_pricing.png" width="500" height="320" /> </center>
* 플랫폼은 `jekyll`를 선택합니다.
    <center> <img src="/assets/images/etc/disqus_choose_platform.png" width="320" height="320" /> </center>
    

##### 5. 상단 `Your Sites` 메뉴 > 내 블로그 선택 > `ORGANIZATION` > `Sites` > `Edit Settings` > `Website URL`을 포함한 세팅을 수정/추가
<center> <img src="/assets/images/etc/disqus_edit_config.png" width="500" height="600" /> </center>

## 내 블로그에 설정하기

##### 1.  _config.yml

```yml
comments:
  provider        : disqus
  disqus:
    shortname     : dev-eunji
```
shortname 의 `dev-eunji`은 아래 방법으로 블로그의 shortname을 확인 후 변경이 필요합니다.
* `가입 및 연동 준비설치하기` > `5. 단계 화면`
* [https://help.disqus.com/customer/portal/articles/466208-what-s-a-shortname-](https://help.disqus.com/customer/portal/articles/466208-what-s-a-shortname-)


##### 2. post 작성시, `commonts` 옵션을 true 로 추가해주면 끝!

```yml
---
# other options
comments: true
---
```

