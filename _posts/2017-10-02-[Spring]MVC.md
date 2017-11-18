---
title: "[Spring] MVC"
description: "JSP for Spring Framework & MVC Pattern"
categories: 
 JSP 
 Spring
 MVC
tags: 
 JSP
 Spring
 MVC
 intellij
date: 2017-10-02 
comments : true
---


# MVC

![MVC패턴](/assets/images/[spring]mvc_pattern.png)

* MVC는 Model View Controller 의 약자로 Application을 세가지 역할로 구분한 개발 방법론이다.

* 사용자 Interface 와 로직을 분리해서 유지보수가 쉽도록 구현했다.

* 사용자가 Controller 사용해서 Model을 통해 데이터를 가져오고 그바탕으로 시각적인 표현을 View가 보여준다.

* WEB에서의 MVC
    1. 사이트 접속
    2. Controller가 Model 호출
    3. Model은 데이터를 제어
    4. Controller가 Model의 결과를 View 반영
    5. View가 보여짐
    
* View Model 사이에 Controller 를 이용해서 의존성을 분리하기 위해서다.
    
## Model
* DB - Class

* 데이터 변경에 따른 내용을 View 를 통해서 업데이트 한다.

## View
* HTML/css/JS

* 화면에 보여지는 로직이고 , Model에 이해서 업데이트 된다.

## Controller
* JAVA(servlet)

* 직접적으로 View를 업데이트를 하지 않습니다.

