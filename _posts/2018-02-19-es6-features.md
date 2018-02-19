---
layout: post
title: "ES6에서 기능들 (1)"
description: "ES6에서 추가/변경된 사항 (1)"
date: 2018-02-19
tags: [javascript, es6]
comments: true
share: true
---

ECMAScript 6. (ECMA2015)로 불리는 이 녀석. 난 자바스크립트가 이렇게나 바뀌었고 이렇게나 활용도가 높아져 있는지 몰랐다. 프론트엔드에 다시 관심을 가지게 된 이후에 다가온 ES6는 나에게 호기심 덩어리가 되어있었다. 그럼 어떠한 요소들이 대표적으로 추가되었는지 정리해보자.

--- 
#### 1. let, const
기존 변수 타입인 var이외에 let과 const 키워드가 추가되었다. var변수가 function 스코프이며 hoisting이 가능한 특징이 있다.
##### 1-1. let
let 키워드는 블록 scope로, 선언된 블록 내에서만 값이 유지되는 특성을 가지고 있다. 
이해 하기 쉽게 아래 예제를 보자
```javascript
var a = 1; let b = 1;

(function some() {
  var a = 2; // 다른 변수
  let b = 2; // 다른 변수
  if(true){
    var a = 3; // 변수 재선언 !
    let b = 3; // 다른 변수
    console.log('a = '+a); // 3
    console.log('b = '+b); // 3
  }
  console.log('a = '+a); // 3 ( 재선언 변수 사용 )
  console.log('b = '+b); // 2
})();
```
위 코드에서의 출력은 처음  if 문 안에 출력은 a와 b 모두 3이 나오지만 if문 밖에서는 a는 3 b는 2라는 값이 출력된다. if문 안에서의 var a = 3; 은 기존의 변수 a의 값을 다시 할당해버리고 if문 밖에서도 유효하게 된다. (function scope) 그러나 let은 그 안에서만 유효하기 때문에 이후의 출력에서 기존에 선언하였던 b의 값을 가져와서 출력해준다.
##### 1-2. const
const는 let과 같은 블록 스코프를 갖지만 차이라고 하면 const는 변수에 할당된 값을 변경할 수 없다는 점이다. 그래서 const변수에 할당된 값은 상수가 된다.
const는 값을 변경할 수 없지만 object나 array 타입의 변수를 통해서 object내의 값을 재할당하여 사용할 수 있다. 
```javascript
const variable1 = 'Hello';
variable1 = 'Yaho!'; // error

const variable2 = { language : '한글' };
variable2.language = '영어'; // OK
```

#### 2. Arror function

#### 3. Spread 연산자 (rest parameter) / Destructuring

#### 4. backtick 

#### 5. 