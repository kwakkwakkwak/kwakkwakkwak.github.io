---
title: "[Disqus] Setting"
description: "disqus는 이렇게 추가합니다."
categories: 
 disqus
 comments
tags: 
 disqus
date: 2017-10-03 
comments :  false
---

Github Blog에 댓글기능을! [Disqus](https://disqus.com/)

### Disqus 회원가입

* [Disqus](https://disqus.com/) 에서 회원 가입을 진행합니다.

![1](/assets/images/disqus/1.png)
![2](/assets/images/disqus/2.png)
(Google,Facebook,twitter 로 쉽게 Sgin in 가능합니다.)


### Disqus 코드 추가

![3](/assets/images/disqus/3.png)
![4](/assets/images/disqus/4.png)
(여러 플랫폼을 제공합니다. 제 블로그는 jekyll 입니다.)

![5](/assets/images/disqus/5.png)
![6](/assets/images/disqus/6.png)
![7](/assets/images/disqus/7.png)

* 위에 과정 중에서 구현에 관련된것은 아래 두개입니다.
{% raw %} `{% if page.comments %}` `{% endif %}` {% endraw %}
if문 사이에 저 코드를 넣어서 
**원하는 곳에 이코드를 추가하세요**   (보통 post.html 일겁니다.)

```
      <div id="disqus_thread"></div>
      <script>

          /**
           *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
           *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
          /*
          var disqus_config = function () {
          this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
          this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
          };
          */
          (function() { // DON'T EDIT BELOW THIS LINE
              var d = document, s = d.createElement('script');
              s.src = 'https://kwak.disqus.com/embed.js';
              s.setAttribute('data-timestamp', +new Date());
              (d.head || d.body).appendChild(s);
          })();
      </script>
      <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
     
```
      
**.md파일**
    
    ---
    # other options
    comments : false/true // true  : comments visibility
                          // false : comments hidden 
    # other options
    ---

* 현재 페이지는 댓글기능을 지원 하지 않게 했습니다.


* [적용페이지](https://kwakkwakkwak.github.io/jsp/spring/mvc/2017/10/11/Spring_Book_Setting/)

![8](/assets/images/disqus/8.png)
