---
title: "[Annotation]@RestController"
description: "Rest 방식 접근의 이해"
categories:
 JSP 
 mvc
 Annotation
tags: 
 RestController
 ResponseBody
 RequestBody
date: 2017-10-03 
comments : true
---

# Rest 방식 접근의 이해

## _`Rest`_ 란?
    
 * URI 정보의 지원
 
 * `HTTP Method` 의 필요 
 
 * 두가지를 합쳐서 `@RestController`안에서 해결
 
 
 
## RestController 는

 * `@ResponseBody`의 생략
   * `@ResponseBody`는 JSP를 View를 사용하지 않고 데이터 그대로를 보여준다.(JSON)형식으로
   
   * Object >> JSON (직렬화) 해서 보여줌
   
 * `HttpStatus`를 추가 `ResponseEntity`의 사용
 
   * `Http Method`의 활용 
  
     * 보통 `GET`, `POST` 만 사용
     
     * `GET`, `POST`, `DELETE`, `PUT`, `Trace`, `Head`, `Content`, `Options`
     
 * URI
   
   * 보통의 URI : https://localhost:8080/sboard/readPage?bno=1 
  
   * data 를 `@RequestParam` 통해서 받음 
   
   * Rest 방식의 URI : https://localhost:8080/replies/1
   
   * data 를 `@PathVariable` 통해서 받음
   
   * 두 방식 모두 객체로 받는 방식은 `@RequestBody`
   
     * `@RequestBody` 는 JSON >> Object (역직렬화) 해서 값으로 사용
