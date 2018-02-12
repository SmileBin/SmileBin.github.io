---
layout: post
title: "Angular 명명규칙/스타일 가이드"
description: "Angular 공식 페이지에서 제안하는 명명규칙 기본"
date: 2018-02-12
tags: [angular, naming]
comments: true
share: true
---

앵귤러에서 파일을 생성하거나 속성에 대한 이름을 명명할 때 어느정도 가이드라인이 있지 않으면 가독성도 떨어지고 보기 답답한 상태의 코드셋이 되어버릴 수 있다 그런 점을 피하기 위해 Angular 공식 사이트에서도 어느정도 가이드 라인을 자세하게 주고 있다. 공식사이트 만큼 자세하진 않지만 어느정도 기본적으로 정리해놓고 자주 보기 위해 기록성으로 남긴다.
[Anuglar style guide 페이지 참조](https://angular.io/guide/styleguide)

--- 
#### 1. 파일명
1) 소문자  
2) kebap-case  
```ts 
honey-apple.component.ts - O  
honeyApple.component.ts - X  
Honey-apple.component.ts - X  
```

#### 2. 타입별 파일명
1) 서비스 : name.service.ts  
2) 컴포넌트 : name.component.ts  
3) 파이프 : name.pipe.ts  
4) 모듈 : name.module.ts  
5) 라우팅 : name.routing.ts  
5) 디렉티브 : name.directive.ts  
6) 일반/클래스 : name.ts  

#### 3. '.ts'파일 내 class 명
1) 첫 글자 대문자  
2) camelCase  
```ts 
hero-list.component.ts : HeroListComponent  
validation.directive.ts : ValidationDirective  
hero.service.ts : HeroService  
```

#### 4. 함수명
1) 첫 글자 소문자  
2) camelCase  
```ts
getHeroList(){}
hoenyApple(){}  
```

#### 5. 라우팅 URL
1) 소문자  
2) kebap-case  
```ts
/hero-detail - O  
/heroDetail - X  
```
#### 6.컴포넌트 선택자
1) 소문자  
2) kebap-case  
3) prefix 선언 지향  
```ts
@Component({ selector : 'interval-dir' }) - O  
@Component({ selector : 'hero' }) - Not good (prefix 선언지향)  
```

#### 7. 디렉티브 선택자
* 태그 
1) 소문자  
2) kebap-case  
3) prefix 선언 지향  
```ts
@Directive({ selector : 'interval-dir' }) - O  
@Directive({ selector : 'intervalDir' }) - X  
```
* 속성
1) 첫 글자 소문자  
2) camelCase  
3) prefix 선언 지향  
```ts
@Directive({ selector : '[addrValidate]' }) - O  
@Directive({ selector : '[validate]' }) - Not good (prefix 선언지향)
```

#### 8. 파이프 선택자
1) 첫 글자 소문자
2) camelCase
```ts
ellipsis.pipe.ts : @Pipe({ name : 'ellipsis' }) 
init-caps.pipe.ts : @Pipe({ name : 'initCaps' })
```


#### 9. 유닛테스트 파일명
1) '.spec'을 '.ts'앞에 붙여줌
```ts
name.component.spec.ts
```

#### 10. 변수
1) 첫 글자 소문자 
2) camelCase
3) '_' 언더바 사용 지양
```ts
let testResult;
const heroDetails;
```

#### 11. const 변수
1) 일반 변수와 명명동일  
2) 값이 변하지 않는 대상에 한해 대문자 사용 및 '_' 언더바 사용 허용 (사용 지양)
```ts
export const heroLists; 
export const MAX_VALUE = 5;
```

#### 12. tslint 사용을 통한 기본 줄맞추기 적용
1) visual studio code plugin 설치를 통한 tslint 적용

#### 13. 컴포넌트 메타데이터내 inputs/outputs를 사용한 변수 선언금지
데코레이터만 사용  
* 옳은 예
```ts
@Component({
  selector: 'toh-hero-button',
  template: `<button>{{label}}</button>`
})
export class HeroButtonComponent {
  @Output() change = new EventEmitter<any>();
  @Input() label: string;
}
```

* 잘못된 예
```ts
@Component({
  selector: 'toh-hero-button',
  template: `<button></button>`,
  inputs: [
    'label'
  ],
  outputs: [
    'change'
  ]
})
```

#### 14. Input/Output 변수와 태그 내 변수 동일하게 구성
* 옳은 예

```ts
@output() temp1;   
@input() temp1;
```

```html
<toh-hero-button label="OK" (change)="doSomething()">
</toh-hero-button>
<!-- `heroHighlight` is both the directive name and the data-bound aliased property name -->
<h3 heroHighlight="skyblue">The Great Bombasto</h3>
```

```ts
// Component
@Component({
  selector: 'toh-hero-button',
  template: `<button>{{label}}</button>`
})
export class HeroButtonComponent {
  // No aliases
  @Output() change = new EventEmitter<any>();
  @Input() label: string;
}
```
* 잘못된 예
```ts
@output('temp1') temp2;
@input('temp1') temp2;
```

```html
// Markup
<toh-hero-button labelAttribute="OK" (changeEvent)="doSomething()">
</toh-hero-button>
```

```ts
// Component
@Component({
  selector: 'toh-hero-button',
  template: `<button>{{label}}</button>`
})
export class HeroButtonComponent {
  // Pointless aliases
  @Output('changeEvent') change = new EventEmitter<any>();
  @Input('labelAttribute') label: string;
}
```

#### 15. 컴포넌트 내 속성선언 시 순서
1) public field  
2) private field  
3) constructor  
4) life-cycle method  
5) public methods  
6) private methods  

#### 16. 'on' prefix 변수할당 금지
1) 이벤트명 형식의 메소드에만 할당
* 옳은 예

```html
<toh-hero (savedTheDay)="onSavedTheDay($event)"></toh-hero>
```

```ts
// Class
export class HeroComponent {
  @Output() savedTheDay = new EventEmitter<boolean>();
}
```
* 잘못된 예

```html
<toh-hero (onSavedTheDay)="onSavedTheDay($event)"></toh-hero>
```

```ts  
// Class
export class HeroComponent {
  @Output() onSavedTheDay = new EventEmitter<boolean>();
}
```

#### 17. 컴퍼넌트 / 서비스 구현 대상범위 명확히
1) 서버접근은 서비스 로직에서만 구현  
2) 화면컨트롤은 컴퍼넌트에서 구현

#### 18. Object 관련 action(CSS control)은 directive로 별도 조정하는 것을 지향
* 옳은 예

```ts
import { Directive, HostBinding, HostListener } from '@angular/core';
 
@Directive({
  selector: '[tohValidator]'
})
export class ValidatorDirective {
  @HostBinding('attr.role') role = 'button';
  @HostListener('mouseenter') onMouseEnter() {
    // do work
  }
}
```

* 잘못된 예

```ts
import { Directive } from '@angular/core';
  
@Directive({
  selector: '[tohValidator2]',
  host: {
    '[attr.role]': 'role',
    '(mouseenter)': 'onMouseEnter()'
  }
})
export class Validator2Directive {
  role = 'button';
  onMouseEnter() {
    // do work
  }
}
```

#### 19. @Inject 대신 @Injectable 사용 
* 옳은 예

```ts
@Injectable()
export class HeroArena {
  constructor(
    private heroService: HeroService,
    private http: Http) {}
}
```

* 잘못된 예

```ts
export class HeroArena {
  constructor(
      @Inject(HeroService) private heroService: HeroService,
      @Inject(Http) private http: Http) {}
}
```
