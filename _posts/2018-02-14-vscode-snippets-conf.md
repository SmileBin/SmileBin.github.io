---
layout: post
title: "VSCode 스니펫 사용하기"
description: "커스텀 스니펫과 프로젝트에서의 공유"
date: 2018-02-13
tags: [vscode, snippet]
comments: true
share: true
---

스니펫은 개발을 하는데에 있어 유용한 툴?기능? 중 하나라고 볼 수 있다. 개발 시 스니펫을 통해서 어느정도 개발 가이드로 공유할 수도 있으며, 그에 따라 프로젝트에서 어느정도의 규격화된 소스코드를 만들어내는 데에도 일조한다. 또한 반복적인 코드를 쉽게 구성하게 해줘 시간단축에도 도움이 많이된다. 물론 필자는 그렇게 많이 이용해 보진 않았지만. <del>내 Ctrl+C,V가 스니펫보다 더 유용해</del>  아무튼 vscode에서의 커스텀 스니펫에 대한 정보를 알아보자

--- 
#### 1. 쉬운 스니펫 구성
가장 쉬운 스니펫 구성은 뭐냐.. 라고 하면 역시 플러그인 설치다. snippet으로 검색하면 언어별로 무수히 많은 스니펫이 나오니 개발 하는 언어에 맞춰 사용하자.  
나같은 경우엔 Angular v5 snippets 를 애용하고 있다. 

#### 2. 커스텀 스니펫 구성
vscode 메뉴에서 '파일 > 기본 설정 > 사용자 코드 조각' 을 선택해 보자. 그럼 언어별로 리스트가 위에 쭈욱 나올 것이다. Angular를 예로 들자면 Angular는 typescript 기반이기 때문에 typescript라고 검색하다보면 목록이 나오니 해당 하는 것을 클릭하면 json 형태의 (정확히는 json 형태가 아니다 주석도 달려 있으니) snippet작성 편집기가 나오게 된다.  
```json
  "Angular Service": {
    "prefix": "a-service",
    "description": "Angular service",
    "body": [
      "import { Injectable } from '@angular/core';",
      "",
      "@Injectable()",
      "export class ${1:Name}Service {",
      "$0",
      "\tconstructor() { }",
      "}"
    ]
  }
```
위의 예시는 'a-service'라고 치고 Ctrl + Space 눌렀을 시 body안에 있는 내용이 자동으로 뿌려지게 된다.  
여기서 $1, $2 등 달러 표시가 있는 쪽에 커서가 자동으로 가게 되고 탭으로 이동이 가능하게 된다.  
참고로 vscode 자체적으로 구성한 snippet은 생성경로가 아래와 같은 형태를 띄고 있어서 프로젝트 내 다른 사람에게 전파하기가 어려운 단점이 있다.  
```
c:\Users\SungBin\AppData\Roaming\Code\User\snippets\typescript.json
```
해결법은 아래에서 확인해보자.

#### 3. 프로젝트에서 snippet 공유하기
플러그인을 깔아야 된다. 내가 추천하는 것은 'Project Snippets'이다.  
위의 플러그인을 깔면 프로젝트내의 스니펫 파일을 프로젝트 소스에서 사용할 수 있게 해주는 역할을 한다.
물론 확장자가 json이기 때문에 vscode 자체 스니펫처럼 주석을 사용하거나 하지는 못한다.
나같은 경우는 .vscode 디렉토리내에 snippet디렉토리를 구성하고 해당 내용을 .gitignore에서 예외 처리하여 프로젝트 인원에게 공유하였다. (물론 프로젝트 인원들도 해당 플러그인이 깔려 있어야 한다.)
