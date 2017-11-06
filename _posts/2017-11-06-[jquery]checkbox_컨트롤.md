---
title: "[jquery] checkbox 컨트롤"
description: "jquery를 이용해서 checkbox 컨트롤"
categories: 
tags: 
date: 2017-11-06
comments : true
---

# 실행화면

* jquery를 이용해서 checkbox 컨트롤

<iframe src ='/assets/src/jquery/checkbox.html' width="100%" height="400"></iframe>


# CODE

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script src="./jquery-3.2.1.min.js"></script>
    <script>
        var checkArr =[];

        var checkbox_click = function checkbox_click(checkbox) {
            if(checkbox.prop("checked"))
                checkArr.push(checkbox.val());
            else {
                for (var i = 0; i < checkArr.length; i++) {
                    if(checkArr[i] == checkbox.val())
                        checkArr.splice(i, 1);
                }
            }
            $("#text").html(checkArr);
        }
        var deselect = function deselect() {
            var len = checkArr.length;
            for(var i = 0 ; i < len ; i++) checkArr.pop();
            $("#text").html(checkArr);
        }
        var allSelect = function allSelect() {
            $("input[type='checkbox']").each(function () {
                if(!$(this).prop("checked"))
                    checkArr.push($(this).val());
            });
            $("#text").html(checkArr);
        }

        $(document).ready(function () {
            //checkbox이 변경될때 console에 log를 남긴다.
            $("input[type='checkbox']").on('change', function () {
                //console.log("sss");
               checkbox_click($(this));
            });
            // 모든 checkbox 선택
            $("#selectAll").on("click", function () {

                allSelect();
                $("input[type='checkbox']").prop('checked',true);

            });
            // 모든 checkbox 해제
            $("#Deselect").on("click", function () {
                deselect();
                $("input[type='checkbox']").prop('checked',false);
            });
        });
    </script>
</head>
<body>
<button id="selectAll">전체 선택</button><button id="Deselect">선택 해제</button> <br>
<div id="chkboxs"></div>
    <input type="checkbox" value="축구">축구 <br>
    <input type="checkbox" value="야구">야구 <br>
    <input type="checkbox" value="농구">농구 <br>
    <input type="checkbox" value="배구">배구 <br>
    <input type="checkbox" value="탁구">탁구 <br>
    <input type="checkbox" value="하키">하키 <br>

<textarea readonly rows="4"cols="60" id="text"></textarea>
</body>

</html>
```

# 어려웠던 점 (`.attr()` & `.prop()`)

* 위에 코드를 짜면서 고생했던 내용이 `attr` & `prop` 이다. 

> .attr()는 HTML 의 속성(Attribute)을 취급
> 
> .prop()는 JavaScript 의 속성(property) 를 취급
 
 * 라고 하는데 그냥 checked 처럼 태그 속성에 값이 존재 하지 않을때 .prop() 를 써야하만 한다.
 
 * 더 간단한 방법은 .attr() 대신에 .prop() 를 쓰면 된다.
 
