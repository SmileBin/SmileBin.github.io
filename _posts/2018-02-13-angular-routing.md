---
layout: post
title: "Angular routing"
description: "Angular 골치아픈 라우팅"
date: 2018-02-13
tags: [angular, routing]
comments: true
share: true
---

앵귤러로 어플리케이션을 구성하다보면 레이아웃별로 라우팅구조를 설계해야 하고 화면구조에 따라 라우팅도 나눠줄 경우도 생기고, lazy load를 위한 모듈별 라우팅 구성도 고려해줘야 한다.
실제로 구성하다보면 심심치 않게 뜨는 max call stack size.. 관련 라우팅 꼬이는 문제도 보이고, 레이지로딩이 안되서 초기로딩이 엄청나게 느려지는 경우도 보게 된다.
아직도 익숙하지 않지만 참 앵귤러를 한다면 꼭 정복해야만 하는 산같은 녀석이다.

--- 
### 1. 모듈과 라우팅 그리고 컴포넌트와의 관계
모듈에는 종류가 있다. 보통 공유모듈이나 코어모듈에는 라우팅이 쓰이지 않고 feature module (특징모듈?)에 라우팅이 쓰이게 되는데 예를 들면 아래와 같다.

```ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';
import { TmpComponent } from './tmp.component';

const routes: Routes = [
    {  path: 'tmp', component: TmpComponent }
];

@NgModule({
    imports: [
        CommonModule,
        RouterModule.forChild(routes)
    ],
    exports: [],
    declarations: [
        TmpComponent
    ],
    providers: []
})
export class TmpModule { }
```

위는 일반적인 모듈에 컴포넌트에 라우팅을 연결해주는 모습이다. ~~~/tmp 경로에 Tmp컴포넌트가 호출되는 구조이다.  
만약 특정 컴포넌트 안의 라우팅 영역으로 별도의 모듈(컴포넌트)를 호출하여 구성하고 싶다면 어떻게 할까?

```ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';
import { TmpComponent } from './tmp.component';
import { Child1TmpComponent } from './children/child1.component';
import { Child2TmpComponent } from './children/child2.component';

const routes: Routes = [
    {   
        path: 'tmp',
        component: TmpComponent,
        childeren: [
            { path: 'child1', component: Child1Component },
            { path: 'child2', component: Child2Component }
        ]
    }
];

@NgModule({
    imports: [
        CommonModule,
        RouterModule.forChild(routes)
    ],
    exports: [],
    declarations: [
        TmpComponent,
        Child1Component,
        Child2Component
    ],
    providers: []
})
export class TmpModule { }
```

위의 예제 소스는 tmp 컴포넌트 영역안에서 지정된 라우팅영역 ( <router-outlet></router-outlet> 으로 선언된 부분 )에 자식 컴포넌트들이 그려지는 구조이다.
해당 자식 컴포넌트에 대한 접근은 tmp/child1, tmp/child2 와 같은 url로 접근이 가능하다.

### 2. lazy loading 처리
사실 앵귤러를 구성하면서 쉽게 적용 가능한 부분이지만 어느단위로 lazy load 처리를 할지가 고민이 되는 부분이다. 앵귤러는 모듈단위로만 lazy load가 된다. 그말인 즉슨, 모듈단위에 대한 정의가 중요해진다는 것이다. 어플리케이션 내에 다양한 카테고리가 생성되고 어플리케이션의 규모가 커지게 되면 lazy load 없이 어플리케이션이 돌아가게 된다면 초기로딩속도가 엄청 나게 느려지는 문제점이 발생한다. 만약 카테고리별로 모듈화 시켜놓고 lazy load 방식으로 처리하게 된다면, 초기로딩 속도가 빨라지고 해당 카테고리 접근 시 약간의 로딩이 걸리는 식으로 처리가 될 것이다. 만약 카테고리가 너무 볼륨이 커지면? 그 안에 컴포넌트 단위로 또 묶어서 모듈로 구성하여 동적 로딩으로 처리하게 해야된다. 어플리케이션의 볼륨과 구조에 따라 모듈을 정의하고 해당하는 모듈을 lazy load하는 식으로 하는게 잘 구조화 된 앵귤러 어플리케이션이 되는 방향이다.

아래는 lazy load의 예시이다.
```ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';
import { Tmp2Component } from './tmp2.component';

const routes: Routes = [
    { path : 'child1', loadChildren : './child-module/tmp-child1.module#TmpChild1Module' },
    {
        path : 'child2',
        component: Tmp2Component,
        loadChildren : './child-module/tmp-child2.module#TmpChild2Module'
    }
];
@NgModule({
    imports: [
        CommonModule,
        RouterModule.forChild(routes)
    ],
    exports: [],
    declarations: [
        Tmp2Component
    ],
    providers: [],
})
export class TmpModule { }
```
lazy load 시에는 원하는 대상 모듈을 'loadChildren' 이라는 프로퍼티로 '경로/모듈파일명#모듈클래스명' 형식으로 넣어줘야 한다.  
여기서 <mark>중요!</mark> 보통 모듈에 선언함으로써 대상 컴포넌트나 디렉티브 서비스를 사용하게 되는데 여기서는 동적로딩 대상 모듈을 import에 넣는 실수를 하지 말아야 한다. 그렇지 않으면 이건 동적로딩이 아니게 되며 부모 모듈 호출 시 자식 모듈까지 모두 로딩이 되게 되버린다. 라우팅에서 설정한거랑 상관없이 동작해버리게 되는 문제가 발생하는 것이다.

참 쉽죠?

### 4. 라우팅 이벤트 받아오기
```ts
  constructor(private router: Router){}
  ...

  this.router.events.subscribe(event => {
    ...
  });
```
앵귤러 내부적으로 이벤트는 rx기반으로 동작하고 여기서도 역시 라우팅 이벤트도 observable 형태로 오기 때문에 subscribe를 통해 이벤트를 받아오고 그에 해당하는 로직처리를 수행할 수 있다.

### 5. 한 화면에 다중 라우팅 영역 구성
이것도 참 골치아픈 녀석이다. 화면에 여러 영역을 지정하고 그 영역별로 동적 로딩을 하게 되는 경우 참 복잡하게 다가왔었다. url도 평소에 보지 못한 형태의 url이라 테스트하기가 어렵기도 했다.

컴포넌트 내 template
```html
<div>
  <div>
    <router-outlet name='tmp1'></router-outlet>
  </div>
  <div>
    <router-outlet name='tmp2'></router-outlet>
  </tab>
  <div>
    <router-outlet name='tmp3'></router-outlet>
  </div>
</div>
```

라우팅 적용
```ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { CommonModule } from '@angular/common';
import { Tmp2Component } from './tmp2.component';

const routes: Routes = [
    { path : 'child1', loadChildren : './child-module/tmp-child1.module#TmpChild1Module', outlet: 'tmp1' },
    { path : 'child2', loadChildren : './child-module/tmp-child2.module#TmpChild2Module', outlet: 'tmp2' },
    { path : 'child3', loadChildren : './child-module/tmp-child3.module#TmpChild3Module', outlet: 'tmp3' }
];
@NgModule({
    imports: [
        CommonModule,
        RouterModule.forChild(routes)
    ],
    exports: [],
    declarations: [],
    providers: [],
})
export class TmpModule { }
```
소스를 보면 <router-outlet name=''></router-outlet> 에 이름을 주게 되면 라우팅 에서 'outlet'이라는 속성으로 부여하게 되면 해당 url이 호출되게 된다.  
URL이 좀 독특한데 `../tmp/(tmp1:child1)` 이러한 식으로 나타나게 된다. 위 예제는 동적 로딩이기 때문에 3개가 모두 로딩이 된다면 URL은 `../tmp/(tmp1:child1//tmp2:child2//tmp3:child3)` 와 같은형식으로 표현된다.
