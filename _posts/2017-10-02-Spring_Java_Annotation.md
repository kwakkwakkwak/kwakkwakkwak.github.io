---
title: "Spring Java Annotation"
description: "Spring java Annotation 종류와 사용법"
categories: 
 java 
 Spring
tags: 
 java
 Spring
 intellij
date: 2017-10-02 
---

사용 예제는 [여기](https://github.com/kwakkwakkwak/Java_Spring) 에서 확인 할 수 있습니다.

# Console Spring framework

## @Autowired

* 자동으로 주입될 때, 실행이 된다.

예제 part5 package
---
MainApp.java
```java
package part5;


import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class MainApp{
    public static void main(String args[]){
        ApplicationContext ac = new GenericXmlApplicationContext("spring/context5.xml");

        Board board = ac.getBean("board", Board.class);
        board.boardWrite();
    }
}
```
context5.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
    <bean id="board" class="part5.BoardCafe"/>
    <bean id="write" class="part5.FreeBoardWrite"/>
</beans>
```
```java
package part5;

import org.springframework.beans.factory.annotation.Autowired;

public class BoardCafe implements Board {
    private Write write;

    @Autowired
    public void setWrite(Write write) {
        this.write = write;
    }
    
//  @Autowrired 로 생략
//
//    public BoardCafe(){
//        this.setWrite(Factory.getWriteInstance());
//    }



    public void boardWrite() {
        write.doWrite();
    }
}

```
---
## @Qualifier

## @Resource

## @Component

## @PostConstruct

## AOP

### AOP - XML

### AOP - Annotation