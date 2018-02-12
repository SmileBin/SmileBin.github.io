---
layout: post
title: "Angular 코어모듈"
description: "Core 모듈? 컴포넌트간의 통신을 위한 방법 하나"
date: 2018-02-12
tags: [angular, module, core-module, core]
comments: true
share: true
---

부모 자식 컴포넌트간의 통신만 하다가 프레임영역과 컨텐츠 영역( router-outlet 아래의 영역 ) 내 컴포넌트와의 통신을 하려니 같은 모듈이 아니라 shared-service로의 통신도 안되고 router-outlet을 뚫고 @input 과 event emitter 로는 값 전달도 안되고.. 아 이거 어찌하나 해야 하다가 책에서 발견한 core module.. 싱글톤 서비스로 앱 전역에서 사용하는 서비스를 생성하여 컴포넌트간의 데이터 통신을 전역으로 하게 해주는 녀석을 사용했다. 간단하지만 알기 전까지는 생각보다 까다롭게 느껴졌던 녀석이였다.  
Angular 공식 [사이트](https://angular.io/guide/singleton-services/)에서도 확인가능하다.

--- 
#### 1. 코어모듈(서비스) 구성
코어모듈은 싱글톤으로 수행되며, 최상위 모듈(app.module.ts)에 선언하여 모든 컴포넌트에서 접근이 가능한 형태로 구성된다.  
```ts
import { BrowserModule } from '@angular/platform-browser';
....

@NgModule({
  imports: [
    BrowserModule,

    // core module
    SharedServiceModule.forRoot()
  ],
  declarations: [
    AppComponent
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
SharedServiceModule.forRoot()가 app.module.ts에 import된 코어 모듈이며, forRoot()를 통해 모듈내 static으로 선언되어있는 forRoot 함수내의 서비스를 싱글톤으로 구성시켜준다.

#### 2. 코어모듈 내 서비스 
코어모듈을 선언하기 위한 용도가 전역에서 접근 가능한 싱글톤 서비스를 구성하기 위한거였다.
아래는 NgModule의 forRoot()를 이용하여 전역서비스로 사용할 서비스를 코어모듈에 등록하는 것이다.  
(NgModul 데코레이터 안에 일반 모듈처럼 컴포넌트, 디렉티브 등을 선언하여 app.module에 import하는 것도 가능)

```ts
import { NgModule, Optional, SkipSelf, ModuleWithProviders } from '@angular/core';
import { SidenavFlagService } from './sidenav-flag/sidenav-flag.service';

@NgModule({ })
export class SharedServiceModule {
  static forRoot(): ModuleWithProviders {
    return {
      ngModule: SharedServiceModule,
      providers: [ SidenavFlagService ]
    };
  }
}
```

#### 3. 사용
코어모듈로 선언하게 되면 전역 서비스로 활성화 되는 만큼, 각각의 하위모듈에서 별도로 서비스를 providers에 선언하지 않아도 사용이 가능하고, 컴포넌트에서 생성자 선언을 통한 주입을 하여 사용하면 된다.  
이건 뭐 일반 서비스랑 다를게 없어서..  아 참고로 컴포넌트에서 코어모듈의 전역서비스의 값을 변경하게 되면 참조하고 있는 다른 컴포넌트에서도 영향을 미칠 수 있으니 한정적으로 사용하는 것이 좋다.

