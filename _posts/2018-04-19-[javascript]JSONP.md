---
title: "[javascript]JSONP(JSON with Padding)"
description: "jsonp 에 대해서 간단하게 알아봅시다"
categories: javascript jsonp crossDomain
tags: javascript jsonp crossDomain
date: 2018-04-19
comments : true
---

# HTTP 접근 제어 (CROS)

[http 접근제어 MDN web docs](https://developer.mozilla.org/ko/docs/Web/HTTP/Access_control_CORS)

* 처음 전송되는 리소스의 도메인과 다른 도메인으로 부터 리소스가 요청될경우 해당 리소스는 cross-origin HTTP 요청에 의해 요청됨.

* 보안상의 이유로, 브라우저들은 스크립트 내에서 초기화되는 corss-origin HTTP 요청을 제한한다.

* 그래서 API를 이용하는 경우는 jsonp를 이용하여 callback 함수에서 해결하는 방법 `JSONP` 를 사용합니다.

# JSONP(JSON with Padding)

* jsonp는 한 웹페이지에서 도메인이 다른 웹페이지로 데이터를 요청할 때 사용하는 자바스크립트 개발 방법론

* `<script>` 태그를 이용해서 동일출처 원칙을 회피합니다. `<script>`는 도메인이 다른 스크립트 파일이라도 embed 가능 하기때문입니다.

## 예제 코드

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="a"></div>
<script>

    const test = ({data:{content}})=>{
        console.log(d64(content));
        const ta = document.createElement('textarea');
        ta.innerText = (d64(content));
       document.getElementById("a").appendChild(ta);
    };
    
    const Github = class{
        constructor(id, repo){
            this._base = `https://api.github.com/repos/${id}/${repo}/contents/`;
        }
        load(path){
            const s = document.createElement('script');
            s.src = `${this._base + path}?callback=test`;
            document.head.appendChild(s);
            document.head.removeChild(s);
        }
    };

    const d64 =v=>decodeURIComponent(atob(v).split('').map(c=>'%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)).join(''));


    const  github1 = new Github('kwakkwakkwak','TypAd');
    github1.load('README.md');
</script>
</body>
</html>

```

* github에서 jsonp로 제공하는 주소로 테스트 코드를 작성했습니다.

* 이코드는 [codespitz](https://www.facebook.com/groups/codespitz/) 에서 가져왔습니다.

