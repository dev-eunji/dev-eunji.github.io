---
title: "[Design Pattern] 퍼싸드 패턴 (The Facade Design Pattern)"
categories: [CS]
tags: [CS, 디자인패턴, Facade, Facade Pattern]
last_modified_at: 2021-01-30
comments: true
---

> 고수준의 통합된 인터페이스를 제공하는 패턴이다.

<img src="/assets/images/cs/design_pattern/facade_pattern.jpeg" width="500" height="500" />

**Facade** 를 구글 이미지에 검색해보면, 여러 건물들의 다양한 외벽(정면) 이미지들이 검색되는 것을 확인할 수 있다.

Facade 패턴도 같은 맥락에서 클라이언트와 내부 서브시스템들을 분리하는 역할을 한다.
내부 여러 서브 시스템들의 복잡한 동작원리를 숨기고, 클라이언트가 쉽게 시스템에 접근하여 사용할 수 있도록 알맞은 인터페이스를 제공하는 패턴이다.

Facade 패턴을 사용하면, 클라이언트는 복잡한 내부 시스템의 동작 원리를 모르더라도 Facade가 제공하는 인터페이스를 통해 필요한 기능들을 사용할 수 있다.

눈여겨 볼 점은 서브 시스템들을 <ins>캡슐화하는 방식으로 동작하지 않는다.</ins> 대신, 서브시스템들을 조합하여 단일화된 인터페이스를 제공하는 방식이다.
