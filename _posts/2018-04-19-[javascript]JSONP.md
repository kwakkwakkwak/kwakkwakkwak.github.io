---
title: "[javascript]JSONP(JSON with Padding)"
description: "jsonp 에 대해서 간단하게 알아봅시다"
categories: javascript jsonp crossDomain
tags: javascript jsonp crossDomain
date: 2018-04-19
comments : true
---


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

