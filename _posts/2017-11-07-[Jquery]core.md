---
title : "[Jquery] 핵심 내용"
description : "javascript 의 핵심"
categories : javascript
tags : javascript jquery core selector event DOM
date : 2017-11-06
comments : true
---


# $() 선택자

 * `"*"` = 전체 / `"#"` = ID / `"."` = Class
 
 * `"h1,h2"` And / `"h1 p"` 후손 / `"h1 > p"` 근접자손 / `"h1 ~ p"` 형제 / `"h+p"` 근접형제
 
 * 요소:입력양식 타입( ex> input:button, input:checkbox, ...)
 
 * 요소:입력양식 상태(`checked`,`disable`,`enable`,`focus`,`input`,`selected`)
 
 
# 검색 & 탐색 

 * 체이닝 용도, `.filter()` / `.find()` : 찾은 노드중에 특정노드 찾기 / 찾은 노드의 자손중에 특정노드 찾기
 
 * `.is()` / `.has()` : 찾는 노드가 맞는지 (return boolean)  / 찾은 노드가 자손으로 가지고 있는지 (return boolean)
  
 * `.eq()` / `.not()` : n번째 노드 접근 / 일치하는 노드 제거
  
 * `.first()` / `.last()` :  첫번째 노드 접근 / 마지막 노드 접근
 
 * `.add()` / `.end()` :  노드 추가 / 이전 선택 노드로
 
 * `get()` : js DOM 객체 찾기
 
 
# 작업

## class

* `.addClass()` `.removeClass()` 클래스 추가와 클래스 삭제

* `.toggleClass()` 클래스가 존재하면 삭제, 존재하지않으면 추가

## attribute

* `.attr()` get/set 속성

* `.removeAttr()` 속성 삭제

* `.prop()` jquery 1.6 부터 사용 `.attr()` 대신 `.prop()`로

## 값

* `.html()`, `.text()`,  `.val()` get/set

## 배열 순회

* `.each()`

> .each(function(){ $(this).작업 })  

## 객체

* `.remove()` 삭제, `.empty()` 속성 삭제/비우기 , `.clone()` (깊은)복사

## 노드

* **선택자.함수(대상)** 
* `.append()`    /  `.appendTo()`       : 대상을 선택자에 마지막 노드로 추가 / 선택자를 대상의 마지막 노드로 추가
* `.prepend()`   /  `.prependTo()`      : 대상을 선택자에 첫번째 노드로 추가 / 선택자를 대상의 첫번째 노드로 추가
* `.after()`     /  `.insertAfter()`    : 대상을 선택자 다음 위치에 추가 / 선택자를 대상의 다음 위치에 추가 
* `.before()`    /  `.insertBefore()`   : 대상을 선택자 이전 위치에 추가 / 선택자를 대상의 이전 위치에 추가

## 부모, 자식

* `.parent()` `.parents()` :  부모노드, 부모노드배열

* `.child()` `.children()` :  자식노드, 자식노드배열

## 객체의 결합

* `$.(Obj, Obj, Obj ...)` : 객체를 결합

# Event

* eventName : 

---
> UI 
>>`blur` `focus` `change` `focusin` `focusout`

> keyboard 
>> `keydown` `keypress` `keyup`

> mouse
>>  `mousedown` `mouseup` `mousemove` `mouseover` `mouseout` 
>>  `mouseenter` `mouseleave` `click` `dblclick`

> form
>> `select` `submit` `change`

> document
>> `load`   `unload` `ready`

> browser
>> `scroll` `error` `resize`
---

## on

* event가 발생할때 eventListener 실행
  
* `.on("eventName",eventListener);` 


## off
* event의 삭제

* `.off("eventName",eventListener)`, `.off("eventName")` , `.off()`

## one

* event가 발생할때 한번만 실행

* `.one("eventName",eventListener)`  

## trigger

* event의 강제실행

* `.triggr("eventName")`, `.triggr("eventName",eventListener)` 


 