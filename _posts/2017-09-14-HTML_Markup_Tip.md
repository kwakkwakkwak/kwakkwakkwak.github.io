---
title: "HTML Markup Tip"
description: "HTML 구조적으로 설계를 잘해보자! 올바른 마크업"
categories: 
 HTML
tags: 
 HTML
date: 2017-09-12
---


# 8가지 올바른 "MarkUp"을 위한 8가지 팁

## HTML속성에 Lnag 속성값을 넣자
 why?! 접근성을 높이기 위해서<br>
 자주쓰는 언어로 만들것
<br><br>
    example)

        <html lang = "en"></html>
        <html lnag = "ko"></html>



## img태그에 alt값 속성 넣기.
why?! 접근성을 높이기 위해서
<br><br>
    example)

        <img src ="" alt = "이미지 설명">



## Sectioning element 태그 내부에 속성값을 적자.
why?! 접근성을 높이기 위해서

   <br><br>
       example)

        <nav class = "anav">
        <h2 class - "sr-only">메뉴</h2>

## img태그와 CSS-background-image
이미지가 정보(Contents)를 가지고 있다면 -> img태그 <br>
그게 아니라면 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; -> CSS

그래도 잘 모르겠다?!

계속 바뀔 수 있는 이미지 -> img태그<br>
그게 아니라면 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;-> CSS를 쓰자

## ul/ol의 자손은 li만 올 수 있다.
List의 요소는 List만 넣자<br><br>
    example)

    -- 맞는 표현 --

    <ul>
        <il>
            <div>
            </div>
        </il>
        <il></il>
        <il></il>
    </ul>

    -- 틀린 표현 --

     <ul>
        <div></div>
            <il></il>
            <il></il>
            <il></il>
     </ul>

## 문단을 구분할땐  < p > 를 쓰자
쓸데없는 < br > 남용을 하지 말자

example)

    -- 틀린 표현 --

    문단<br><br>
    문단<br><br>
    문단<br><br>
    문단<br><br>

    -- 맞는 표현 --

    <p>문단</p>
    <p>문단</p>
    <p>문단</p>
    <p>문단</p>

## 인라인 태그에 블록 태그를 넣지말자
< a >태그는 예외적으로 인라인 태그를 블록태그로 바꿀 수 있다

example)

    -- 틀린 표현 --

    <span>
        <div>
        </div>
    </span>

    -- 맞는 표현 --

    <a href = "이동할 경로"><img src = "이미지 경로" alt = "이미지">

## 인라인 스타일을 쓰지말자

example)

    -- 틀린 표현 --

    <html lang = "ko">
        <body>
            <p class = "example">이렇게 쓰기<p>
        </body>
        </html>
    <p style ="font-size = 16px">이렇게 쓰지마</p>

    -- 맞는 표현 --

    <html lang = "ko">
    <head>
        <style>
        .eample {
            font-size = 16px;
        }
        </style>
    </head>
    <body>
        <p class = "example">이렇게 쓰기<p>
    </body>
    </html>