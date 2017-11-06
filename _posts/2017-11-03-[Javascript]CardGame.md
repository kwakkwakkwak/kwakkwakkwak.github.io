---
title : "[javascript]CardGame"
description: "javascript 로 짜는 간단한 카드게임"
categories: javascript
tags: javascript CardGame 
date: 2017-11-03 
comments : true
---

* 자바스크립트를 공부하면서 간단한 로직의 게임을 만들어 보았다.

# 실행화면

* 간단하게 모두 뒤집으면 시작되는 게임입니다.

* 같은 모양의 카드를 뒤집으면 화면에서 뒤집에진 상태가 고정되고, 눌리지 않는다.

<iframe src ='/assets/src/jsCardGame/card.html' width="100%" height="500"></iframe>

# CODE

```html
<!DOCTYPE html>
<html lang="en">
<head>


    <meta charset="UTF-8">
    <title>Title</title>
</head>
<style>


</style>
<body>
<div id="cards">
    <img src="resource/A_1.jpg" class="A" width="100" height="150">
    <img src="resource/2_1.jpg" class="2" width="100" height="150">
    <img src="resource/3_1.jpg" class="3" width="100" height="150">
    <img src="resource/4_1.jpg" class="4" width="100" height="150">
    <img src="resource/5_1.jpg" class="5" width="100" height="150">
    <img src="resource/6_1.jpg" class="6" width="100" height="150">
    <img src="resource/7_1.jpg" class="7" width="100" height="150">
    <img src="resource/8_1.jpg" class="8" width="100" height="150">
    <img src="resource/9_1.jpg" class="9" width="100" height="150">
    <img src="resource/10_1.jpg" class="10" width="100" height="150">
    <img src="resource/A_1.jpg" class="A" width="100" height="150">
    <img src="resource/2_1.jpg" class="2" width="100" height="150">
    <img src="resource/3_1.jpg" class="3" width="100" height="150">
    <img src="resource/4_1.jpg" class="4" width="100" height="150">
    <img src="resource/5_1.jpg" class="5" width="100" height="150">
    <img src="resource/6_1.jpg" class="6" width="100" height="150">
    <img src="resource/7_1.jpg" class="7" width="100" height="150">
    <img src="resource/8_1.jpg" class="8" width="100" height="150">
    <img src="resource/9_1.jpg" class="9" width="100" height="150">
    <img src="resource/10_1.jpg" class="10" width="100" height="150">
</div>
<button id="turn">뒤집기</button>
<button id="hide">숨기기</button>
<button id="show">보이기</button>
<button id="shuffle">셔플</button>

<script>
    var turnAll = false;
    var cards = document.getElementById('cards').children;
    var btn_turn = document.getElementById('turn');
    var btn_hide = document.getElementById('hide');
    var btn_show = document.getElementById('show');
    var btn_shuffle = document.getElementById("shuffle");
    // 모든 카드 뒤집기
    btn_turn.onclick = function () {
        if(turnAll){ // 카드가 뒷면이면...
            turnAll = !turnAll;
            for(var i = 0 ; i < cards.length ; i++) {
                cards[i].src = "resource/"+cards[i].className+"_1.jpg";
                cards[i].name = 'turnUp';
            }
        }
        else { // 카드가 앞면이면...
            turnAll = !turnAll;
            for(var i = 0 ; i < cards.length ; i++) {
                cards[i].src = 'resource/back.jpg';
                cards[i].name = 'turnOff';

            }
        }
    }

    btn_show.onclick = function () { // 카드 보이기
        for(var i = 0 ; i < cards.length ; i++) {
            cards[i].style.visibility = "visible";
        }
    };
    btn_hide.onclick = function () { // 카드 가리기
        for(var i = 0 ; i < cards.length ; i++) {
            cards[i].style.visibility = "hidden";
        }
    };

    btn_shuffle.onclick = function () { // 셔플
        //console.log(cards.length);
//        for(var i = 0 ; i < cards.length ; i++) {
//            cards[i].style.visibility = "hidden";
//        }
//        // 카드를 가립니다
        console.log();
        for(var i = 0 ; i < cards.length ; i++){ // 카드 배열만큼 for 문 돌림

            var index = Math.ceil(Math.random()*(cards.length-1)); // index 값 random 으로 저장
            var temp = cards[index].src; // temp에 데이터 값저장
            cards[index].src = cards[i].src;
            cards[i].src = temp; // src 값 switching

            // className 값도 switching  ==> src값이 바뀔때 같이 바뀌어야나중에 비교할때씀

            temp = cards[index].className;
            cards[index].className = cards[i].className;
            cards[i].className = temp;

            temp = cards[index].name;
            cards[index].name = cards[i].name;
            cards[i].name = temp;
        }
//        카드를 보입니다. 카드를 뒤집어서 섞은 후에 확인 할려고 나중에  css추가 예정
//        for(var i = 0 ; i < cards.length ; i++) {
//            cards[i].style.visibility = "visible";
//        }
    }
    var turnUpCard = function (card)  { // 카드를 보임
        card.src = "resource/"+card.className+"_1.jpg";
        card.name = 'turnUp';
    }
    var turnOffCard = function (card) { // 카드를 덮음
        card.src = 'resource/back.jpg';
        card.name = 'turnOff';

    }

    var cnt = 1; //
    var fristcard = null;
    var secendcard = null;
    var gaming = false;


    for(var i = 0 ; i < cards.length ; i++) {

        cards[i].onclick = function () {
            if (!gaming) {
                if(this.name != 'turnOff')
                    return;
                //console.log(cnt);
                turnUpCard(this);
                if (cnt == 2) {
                    //cnt = 5;
                    secendcard = this;
                    if (fristcard == secendcard)
                        return;
                    gaming = true;
                    setTimeout(function () {
                        if (!(fristcard.className == secendcard.className)) {
                            turnOffCard(fristcard);
                            turnOffCard(secendcard);
                            //alert("fristcard : " + fristcard.className + ",secendcard : " + secendcard.className);
                        }
                        cnt = 1;
                        gaming = false;
                    }, 1500);
                }
                else if (cnt == 1) {
                    fristcard = this;
                    console.log(fristcard.className);
                    cnt++;
                }
//            else if(cnt == 3)
//                cnt = 1;
            }
        };
    }



</script>
</body>
</html>

```

# 어려웠던 점

* `setTimeout`을 쓸때, 연속으로 3개를 누르면 2번째 값이 누락되는 현상이 있었다.

    > 해결 그래서 gaming 이라는 변수를 선언해서 해결했다.

