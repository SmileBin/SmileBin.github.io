---
layout: post
title: "Rxjs 알아가기"
description: "Rxjs를 써오면서.. 아직 어려운 녀석"
date: 2018-03-16
tags: [angular, rxjs, subject]
comments: true
share: true
---

Rxjs 는 ReactiveX의 자바스크립트 버전이다. 나는 Anuglar를 통해 처음 접해보았지만, Angular만이 아닌 RxJava나 RxSwift 등 여러 언어도 지원하는 것으로 보인다.
난 앵귤러를 하면서 Promise 대신의 용도로 http 서비스 콜을 비동기 처리하는 부분과, 입력하는 이벤트를 감지하여 처리하는 부분 (debouncetime을 준 검색기능) 과 subject를 이용하여 공통을 구현하여 여러 화면에서 하나의 이벤트를 주시하여 데이터 처리 하는 부분 등을 수행했다. 하지만 하면서도 햇갈리고 자세히 알지 못하는 부분이 많아 좀더 자세히 보려한다.  
소개페이지에 보면 “Observable 시퀀스와 표현력있는 쿼리 연산자를 사용하는 비동기적, 이벤트 기반의 프로그램을 구성하기 위한 라이브러리의 집합” 이라고 나와있다.  
그 의미를 이해하려면 'Observer/Observable', 'Stream', 'Subject'등에 대해 어느정도 알고있어야 개념정리가 될 것 같다.  
[참조](http://reactivex.io/documentation/ko/observable.html)

--- 
### 1. Observer / Observable


```ts
```



### 2. Event Stream
```ts
```


### 3. Subject
