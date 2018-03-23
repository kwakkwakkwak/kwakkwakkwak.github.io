---
title: "[DesignPattern]Template-Method-Pattern"
description: "템플릿 메소드에 대해서 자세히 알아보자"
categories: DesignPattern TemplateMethodPattern
tags: DesignPattern TemplateMethodPattern
date: 2018-03-23
comments : true
---

# 템플릿 메소드 패턴 개요

*  상속을 통해 기능을 확장하는 패턴
*  구체적인 것은 하위클래스에서 처리하도록 하는 구조
*  상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 대표적인 방법
*  어떤 알고리즘에 대한 큰 틀이 결정된 상태에서 구체적인 설계를 서브클래스에 맡기는 디자인 패턴
*  상위클래스(템플릿)에 기본적인 로직의 흐름 구성, 그 일부를 추상 메소드·protected 메소드로 기술하여, 서브클래스에서 구현·오버라이딩하도록 하는 패턴
*  스프링 프레임워크 등 프레임워크에서 자주 사용되는 구조

라고 [위키](https://zetawiki.com/wiki/Template_Method_%ED%8C%A8%ED%84%B4)에 나와있다.

# Example

* 처음 큰틀에서 game 클래스를 생성

* 템플릿 메소드인 `playOneGame`를 만들고 `PrintWinner`, `initializeGame` 등 훅 메소드를 만든다.

* 물론 훅 메소드는 여러개 만들수 있다.

```java

abstract class Game {
 
    protected int playersCount;
    
    
    // methods
    abstract void initializeGame();
    abstract void makePlay(int player);
    abstract boolean endOfGame();
    abstract void printWinner(); 
 
    /* A template method : */
    public final void playOneGame(int playersCount) {
        this.playersCount = playersCount;
        initializeGame();
        int j = 0;
        while (!endOfGame()) {
            makePlay(j);
            j = (j + 1) % playersCount;
        }
        printWinner();
    }
}
 ```
 
 * `Game`클래스를 상속 받아서 `Monopoly`, `Chedss` 를 만든다. 
 
 * 큰 틀에서는 다른게 없다.
 
 * 그래서 나머지 훅 메소드를 완성시키면 된다.
  
 
 ```java
 
class Monopoly extends Game {
 
    /* Implementation of necessary concrete methods */
    void initializeGame() {
        // Initialize players
        // Initialize money
    }
    void makePlay(int player) {
        // Process one turn of player
    }
    boolean endOfGame() {
        // Return true if game is over 
        // according to Monopoly rules
    }
    void printWinner() {
        // Display who won
    }
 
}
 
class Chess extends Game {
 
    /* Implementation of necessary concrete methods */
    void initializeGame() {
        // Initialize players
        // Put the pieces on the board
    }
    void makePlay(int player) {
        // Process a turn for the player
    }
    boolean endOfGame() {
        // Return true if in Checkmate or 
        // Stalemate has been reached
    }
    void printWinner() {
        // Display the winning player
    }
 
}
```

* 이러한 디자인 패턴을 템플릿메소드 패턴이라고 한다.
