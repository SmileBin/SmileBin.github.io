---
layout: post
title: "Reactive Programming : RxJS 알아가기"
description: "RxJS를 써오면서.. 아직 어려운 녀석"
date: 2018-03-16
tags: [angular, rxjs, subject]
comments: true
share: true
---

Rxjs 는 ReactiveX의 자바스크립트 버전이다.(**R**eactive e**x**tensions library for **J**ava**S**cript) 나는 Anuglar를 통해 처음 접해보았지만, Angular만이 아닌 RxJava나 RxSwift 등 여러 언어도 지원하는 것으로 보인다.
앵귤러를 하면서 Promise 대신 비동기 http 서비스 콜을 처리하는 부분과, 검색 단어를 타이핑하는 이벤트를 감지하여 처리하는 부분 (debouncetime을 준 검색기능) 과 subject를 이용하여 컴포넌트간 이벤트 기반의 실시간 데이터 통신 등을 수행했다. 하지만 하면서도 헷갈리고 자세히 알지 못하는 부분이 많아 좀더 자세히 보려한다.  
소개페이지에 보면 “Observable 시퀀스와 표현력있는 쿼리 연산자를 사용하는 비동기적, 이벤트 기반의 프로그램을 구성하기 위한 라이브러리의 집합” 이라고 나와있다.  
그 의미를 이해하려면 'Observer/Observable', 'Stream', 'Subject'등에 대해 어느정도 알고있어야 개념정리가 될 것 같다.  
[참조](http://reactivex.io/documentation/ko/observable.html)

--- 
### 1. Observer / Observable
옵저버와 옵저버블 이 두개의 단어를 단순하게 바라본다면 쉽게 다가갈 수 있다. 관찰하는 사람과 관찰할만한(?) 것. 옵저버블이 이벤트를 발생시키면 옵저버는 옵저버블을 구독하고 있다가 해당 이벤트에 대한 것에 반응 한다.  
위와 같은 단순한 패턴을 코드상에 적용하게 된다면 HTTP 리퀘스트 함수를 옵저버블형태로 옵저버가 관찰하게 되면, 응답이 오는 것을 구독하여 해당 결과값을 처리할 수 있다. (여기서 구독이란 subscribe( result => {} ); 함수를 의미) 
옵저버블은 이벤트/데이터를 지니며 연속된 값들을 지니고 있기도 한다. 그래서 스트림을 만들 수 있다고도 하며, 이 데이터를 가지고 가공(병합, Split, Mash 등)이 가능하다. 예를 들면 검색창에 사용자가 입력하는 입력 글자 하나하나를 확인하여 검색을 실시간으로 하거나 입력텀을 계산하여 입력이 멈추면 검색을 시작한다거나 하는 다양한 처리가 가능하다.

#### 1-1. Observer/Observable 동작

일반적인 함수는 아래와 같이 동작한다.
- 메서드를 호출한다.
- 메서드가 리턴한 값을 변수에 저장한다.
- 결과 값을 가진 변수를 통해 필요한 연산을 처리한다.

```ts
// 메서드를 호출하고, 리턴 값을 `returnVal`에 할당한다
returnVal = someMethod(itsParameters);
// returnVal을 통해 필요한 작업을 진행한다
```

비동기 모델에서는 아래와 같은 흐름대로 코드가 실행된다.

- 비동기 메소드 호출로 결과를 리턴받고 필요한 동작을 처리하는 메서드를 정의한다: 이 메서드는 옵저버의 일부가 된다.
- Observable로 비동기 호출을 정의한다.
- 구독을 통해 옵저버를 Observable 객체에 연결 시킨다(또한, 동시에 Observable의 동작을 초기화 한다).

이를 코드로 구현하면 아래와 같다:
```javascript
// 옵저버의 onNext 핸들러를 정의한다, 하지만 실행하지는 않는다
// (이 예제에서는, 단순히 옵저버에 onNext 핸들러만 구현한다)
def myOnNext = { it -> /* 필요한 연산을 처리한다 */ };
// Observable을 정의하지만, 역시 실행하지는 않는다
def myObservable = someObservable(itsParameters);
// 옵저버가 Observable을 구독한다. 그리고 Observable을 실행한다
myObservable.subscribe(myOnNext);
// 필요한 코드를 구현한다
```

#### 1-2. onNext, onCompleted, 그리고 onError
Subscribe 메서드를 통해 옵저버와 Observable을 연결하며, 아래 onNext/onError/onCompleted 매서드를 통해 동작을 정의한다.
- onNext  
Observable은 새로운 항목들을 배출할 때마다 이 메서드를 호출한다. 이 메서드는 Observable이 배출하는 항목을 파라미터로 전달 받는다.
- onError  
Observable은 기대하는 데이터가 생성되지 않았거나 다른 이유로 오류가 발생할 경우 오류를 알리기 위해 이 메서드를 호출한다. 이 메서드가 호출되면 onNext나 onCompleted는 더 이상 호출되지 않는다. onError 메서드는 오류 정보를 저장하고 있는 객체를 파라미터로 전달 받는다.
- onCompleted  
오류가 발생하지 않았다면 Observable은 마지막 onNext를 호출한 후 이 메서드를 호출한다.  

onNext는 0번 이상 호출 될 수 있으며 그 후에는 onCompleted 또는 onError 둘 중 하나를 마지막으로 호출한다. 단, 이 둘 모두를 호출하지는 않는다.  

예시
```javascript
def myOnNext     = { item -> /* 필요한 연산을 처리한다 */ };
def myError      = { throwable -> /* 실패한 호출에 대응한다 */ };
def myComplete   = { /* 최종 응답 후 정리 작업을 한다 */ };
def myObservable = someMethod(itsParameters);
myObservable.subscribe(myOnNext, myError, myComplete);
// 필요한 코드를 계속 구현한다
```

#### 1-3. Angular 예시
마지막으로 RxJS를 기본 비동기 처리 라이브러리로 사용하고 있는 Angular에서의 예를 살펴보자.  
Angular에서의 심플한 예
```ts
// observable 객체 변수는 뒤에 '$'를 붙여준다.
const one$ = new Observable(observer => {
  observer.next(1);
  observer.complete();
});

one$.subscribe( result => {
  console.log(result) // 1
});
```
```ts
// Create simple observable that emits three values
const myObservable = Observable.of(1, 2, 3);

// Create observer object
const myObserver = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};

// Execute with the observer object
myObservable.subscribe(myObserver);
// Logs:
// Observer got a next value: 1
// Observer got a next value: 2
// Observer got a next value: 3
// Observer got a complete notification
```

<!-- #### Hot/Cold Observable
Observable은 항목들을 언제 배출할까? 이건 Observable에 따라 다르다. 옵저버블은 생성되자 마자 항목을 배출하는 'Hot' 옵저버블과 옵저버가 구독할 때 까지 항목을 배출하지 않는 'Cold' 옵저버블로 나뉜다.  
| Hot Observables | Cold observables |
|:-----|----:|
| Observer가 구독하든 말든 상관없이 아이템을 발행  | Observer가 구독해야지 아이템을 발행  | -->


### 2. Subject
Subject는 Observable 과 Observer 둘 다 될 수 있는 특별한 형태로 Subject는 Observables을 subscribe(구독) 할 수 있고 다시 emit(방출)할 수도 있다. Subject에 값을 담아서 가지고 있다가 observable 형태로 제공해주기도 하며, Observer로써 next함수를 통해 구독자에게 값을 넘겨주는 역할을 하기도 한다. 나는 Angular에서 두 개의 컴포넌트가 값을 공유하면서 공유 시 이벤트전달도 서로 하는 용도로 Subject를 사용을 주로 했으며, 아주 편리하게 사용했다.

![Subject와 Observer관계](https://smilebin.github.io/images/subject-observer.png)


#### 2-1. Subject 종류
Subject는 4종류로 분류된다.
- AsyncSubject  
AsyncSubject는 소스 Observable로부터 배출된 마지막 값 emit(배출)하고 소스 Observalbe의 동작이 완료된 후에야 동작한다. (만약, 소스 Observable이 아무 값도 배출하지 않으면 AsyncSubject 역시 아무 값도 배출하지 않는다.)
![AsyncSubject](https://smilebin.github.io/images/S.AsyncSubject.png)
- BehaviorSubject  
BehaviorSubject는 PublishSubject와 거의 같지만 BehaviorSubject는 반드시 값을 초기화를 해줘야 하며 BehaviorSubject는 Observer에게 subscribe하기전 마지막 이벤트 혹은 초기 값부터 emit 하게 된다.
![BehaviorSubject](https://smilebin.github.io/images/S.BehaviorSubject.png)
- PublishSubject  
PublishSubject는 subscribe 전의 이벤트는 emit하지 않습니다. subscribe한 후의 이벤트만을 emit합니다. 그리고 에러 이벤트가 발생하면 그 후의 이벤트는 emit 하지 않습니다.  
![PublishSubject](https://smilebin.github.io/images/S.PublishSubject.png)
- ReplaySubject  
ReplaySubject는 미리 정해진 사이즈 만큼 가장 최근의 이벤트를 새로운 Subscriber에게 전달한다.
![ReplaySubject](https://smilebin.github.io/images/S.ReplaySubject.png)


#### 2-2. Angular 적용 예시
아래는 기본 Subject를 이용한 심플한 예시다.
```ts
// Service
@Service

...

transferSubject: Subject<any> = new Subject<any>();

setObj(inData: any) {
    this.transferSubject.next(inData);
}

getObj(): Observable<any> {
    return this.transferSubject.asObservable();
}


// Component1
// 데이터를 서비스의 함수를 호출하여 전달하며, 이벤트를 바로 발생시켜 컴포넌트 2에서 바로 로직을 실행시키는 트리거 역할을 해줌
this.transferService.setObj({hello: 'World!'});


// Component2
// 서비스의 데이터 저장을 구독하여, 데이터가 저장되면 해당하는 값을 구독하여 바로 콘솔로 찍어주는 기능.
this.transferService.getObj().subscribe(result => console.log(result));

```

### 3. 기타..
RxJS를 사용하여 Angular 내에서 다양한 컴포넌트들이 공통의 데이터의 업데이트에 대한 이벤트를 구독하여 공통적으로 처리하는 기능이나 다양한 유틸성 기능을 구현하는 예제는 향후 포스트에서 다루기로 하자.