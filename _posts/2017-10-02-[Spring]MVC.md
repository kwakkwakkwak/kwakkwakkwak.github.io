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

![MVC 패턴](/assets/images/[spring]mvc/mvc_pattern.png)

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

>    일반적으로 데이터베이스를 통치하는게 이나고, 그에 상응하는 클래스 or 도메인을 뜻한다.

* 데이터 변경에 따른 내용을 View 를 통해서 업데이트 한다.

## View
* HTML/css/JS

> HTML + CSS / JS(JQUERY) 로 화면 만든다.

* 화면에 보여지는 로직이고 , Model에 이해서 업데이트 된다.

## Controller
* JAVA(servlet)

> Spring 에서는 JAVA 를 사용하고 @(Annotation)을 사용해서 역할을 구분한다.
>
> DAO , DAOImpl , Service, ServiceImpl, Controller 의 역할  

* 직접적으로 View를 업데이트를 하지 않는다.

