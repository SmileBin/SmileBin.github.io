---
layout: post
title: "Angular 코어모듈"
description: "Core 모듈? 컴포넌트간의 통신을 위한 방법 하나"
date: 2018-02-12
tags: [angular, module, core-module, core]
comments: true
share: true
---

부모 자식 컴포넌트간의 통신만 하다가 프레임영역과 컨텐츠 영역( router-outlet 아래의 영역 ) 내 컴포넌트와의 통신을 하려니 같은 모듈이 아니라 shared-service로의 통신도 안되고 router-outlet을 뚫고 @input 과 event emitter 로는 값 전달도 안되고.. 아 이거 어찌하나 해야 하다가 책에서 발견한 core module.. 싱글톤 서비스로 앱 전역에서 사용하는 서비스를 생성하여 컴포넌트간의 데이터 통신을 전역으로 하게 해주는 녀석을 사용했다. Angular 공식 [사이트](https://angular.io/guide/singleton-services/)에서도 확인가능하다.

--- 
### 1. 코어모듈(서비스) 구성