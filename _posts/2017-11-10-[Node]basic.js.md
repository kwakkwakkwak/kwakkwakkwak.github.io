---
title: "[Node] Basic"
description: "Node.js 란..." 
categories: 
    javascript
    node
tags: 
    node
date: 2017-11-04
comments : true
---


# [Node.js](https://nodejs.org/ko/)
 
* 비동기 이벤트 주도 JavaScript 런타임으로써 Node는 확장성 있는 네트워크 애플리케이션을 만들 수 있도록 설계되었습니다.
 다음 "hello world" 예제는 다수의 연결을 동시에 처리할 수 있습니다.
 각 연결에서 콜백이 실행되는데 실행할 작업이 없다면 Node는 대기합니다.(라고 홈페이지에 설명이 되어있습니다....)
 
* NodeJS 는 구글 크롬의 자바스크립트 엔진 (V8 Engine) 에 기반해 만들어진 서버 사이드 플랫폼입니다.

## 특징

* 단일 스레드

* javascript V8 Engine 의 빠른 속도

* 비동기, 넌블로킹 IO

* 이벤트 기반 프로그래밍

## NVM

* 노드의 여러버전을 복수로 설치해 쉽게 관리 할 수 있고 특정버전을 선택하여 사용 가능

### 설치

> $ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.4/install.sh | bash

### nvm 명령어

* 최신버전 설치

> $ nvm install node 

* 특정버전 설치

> $ node install 특정버전

* 현재 버전 확인

> $ node --version

* 모든 버전 

> $ node ls-remote

* 설치된 버전 리스트

> $node ls

* 특정 버전 사용

> $ node use 특정버전

* 기본 버전 설정

> node alias default 특정버전

## npm(Node Packaged Modules)

* node 패키지 모듈 저장소

* 패키지 설치 및 호환성 관리를 할수있는 커맨드라인 유틸리티

* npm 최신 버전 설치

> $ sudo npm install npm -g

### npm 자주쓰는 명령어

* npm 프로젝트 시작 (package.json 생성)

> $ npm init

* Option

    * -y : npm install 할때 save 옵션을 적지 않아도 `package.json`에 자동 저장

* npm 패키지 설치

> $ npm install

* npm 패키지 업데이트

> $ npm update

* Option

   * --save -S : package.json 에 추가
    
   * --save-dev -D : devDependencies 에 추가
    
   * -global -g : 글로벌로 설치

* 설치된 npm 패키지 보기

> $ npm ls

* npm 패키지 찾기

> $ npm search

* npm 캐시 지우기

> $ npm cache clean

* npm 재설치 

> $ npm rebuild


**출처, 도움받은 site**

[http://cheekee.co.kr/](http://cheekee.co.kr/?p=58)

[https://velopert.com/](https://velopert.com/133)