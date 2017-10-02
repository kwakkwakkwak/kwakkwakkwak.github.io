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

_사용 예제는 [여기](https://github.com/kwakkwakkwak/Java_Spring) 에서 확인 할 수 있습니다._

## @Autowired (예제 part5,6)

* 생성자와 setter 생략

**MainApp.java**
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

**context5.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">


    <!-- intellij에서 아래부분만 적용해서 improt하면 웨에 xmlns부분도 자동 등록된다-->
    <context:annotation-config/>
    <bean id="board" class="part5.BoardCafe"/>
    <bean id="write" class="part5.FreeBoardWrite"/>
</beans>
```

**BoardCafe.java**
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
**BoardCafe.java**
```java
package part6;

import org.springframework.beans.factory.annotation.Autowired;

public class BoardCafe implements Board {
    @Autowired
    private Write write;

//    변수쪽에 넣는것으로 생략
//    public void setWrite(Write write) {
//        this.write = write;
//    }
//    
//    @Autowrired 로 생략
//
//    public BoardCafe(){
//        this.setWrite(Factory.getWriteInstance());
//    }



    public void boardWrite() {
        write.doWrite();
    }
}
```
## @Qualifier (예제 part7)

* Annotation을 이용한 자동 설정제한

**QnABoardWrite**

```java
package part7;

public class QnABoardWrite implements Write{

    private String sBoardName;

    public QnABoardWrite(){
        init();
    }

    private void init() {
        this.setsBoardName("Q&A Board");
    }

    private void setsBoardName(String sBoardName) {
        this.sBoardName = sBoardName;
    }

    @Override
    public void doWrite() {
        System.out.println(this.getsBoardName() + "Write down!!");
    }

    public String getsBoardName() {
        return sBoardName;
    }
}


```

**context7.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean id="board" class="part7.BoardCafe"/>
    <bean id="freeWrite" class="part7.FreeBoardWrite"/>
    <bean id="qnaWrite" class="part7.QnABoardWrite"/>
</beans>
```

****
```java
package part7;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

public class BoardCafe implements Board {


    @Qualifier("freeWrite") // -> FreeBoard Write down!!
    //@Qualifier("qnaWrite") // -> QnABoard Write down!!
    @Autowired
    private Write write;

    public void setWrite(Write write) {
        this.write = write;
    }

//    public BoardCafe(){
//        this.setWrite(Factory.getWriteInstance());
//    }



    public void boardWrite() {
        write.doWrite();
    }
}

```

* Bean에서 id값을 찾아서 그 클래스만 불러올수 있다.


## @Resource (part 8)

* @Autowired, @Qulifier를 @Resource 로 변경

**BoardCafe.java**
```java
package part8;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

import javax.annotation.Resource;

public class BoardCafe implements Board {


    @Resource(name = "freeWrite")
    private Write write;

    public void setWrite(Write write) {
        this.write = write;
    }

//    public BoardCafe(){
//        this.setWrite(Factory.getWriteInstance());
//    }



    public void boardWrite() {
        write.doWrite();
    }
}

```

* 기본적으로 다른 Bean을 참조해서 사용하는 경우 @Autowired 또는 @Resource를 사용한다.


 | \ | @Autowired  | @Resource  |
 | --- | --- | --- |
 | 객체선택 기준 | Type | Name |
 | 지원 |  Spring Framework | Spring Framework 2.5.* |
 | 입력  Parameter | 여러개 | 1 개 |
 
## @Component (part 9)

**context9.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="part9"/>

    <!--<bean id="board" class="part8.BoardCafe"/>-->
    <!--<bean id="freeWrite" class="part8.FreeBoardWrite"/>-->
    <!--<bean id="qnaWrite" class="part8.QnABoardWrite"/>-->
</beans>
```

**QnABoardWrite**
```java
package part9;

import org.springframework.stereotype.Component;

@Component("qnaWrite")
public class QnABoardWrite implements Write {

    private String sBoardName;

    public QnABoardWrite(){
        init();
    }

    private void init() {
        this.setsBoardName("Q&A Board");
    }

    private void setsBoardName(String sBoardName) {
        this.sBoardName = sBoardName;
    }

    @Override
    public void doWrite() {
        System.out.println(this.getsBoardName() + "Write down!!");
    }

    public String getsBoardName() {
        return sBoardName;
    }
}

```

**FreeBoardWrite**
```java
package part9;

import org.springframework.stereotype.Component;

@Component("freeWrite")
public class FreeBoardWrite implements Write {
    private String sBoardName;

    public FreeBoardWrite(){
        init();
    }
    public void init(){
        this.setsBoardName("Free Board");
    }
    public void setsBoardName(String sBoardName){
        this.sBoardName = sBoardName;
    }
    public String getsBoardName(){
        return sBoardName;
    }
    public void doWrite() {
        System.out.println(this.getsBoardName() + "Write Down!!");
    }
}
```
**BoardCafe**
```java
package part9;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;

@Component("board")
public class BoardCafe implements Board {


    @Resource(name = "qnaWrite")
    @Autowired
    private Write write;

    public void setWrite(Write write) {
        this.write = write;
    }

//    public BoardCafe(){
//        this.setWrite(Factory.getWriteInstance());
//    }



    public void boardWrite() {
        write.doWrite();
    }
}

```

* XML파일을  context:component-scan 을 추가해서 간단하게 만들고 Bean에 객체를 검색해서 주입한다.

## @PostConstruct & @PreDestory (part 10)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
    <bean id="board" class="part10.BoardCafe"/>
    <bean id="write" class="part10.FreeBoardWrite"/>
</beans>
```

```java
package part10;

import javax.annotation.PostConstruct;

public class FreeBoardWrite implements Write {
    private String sBoardName;

//    public FreeBoardWrite(){
//        init();
//    }
    @PostConstruct
    public void init(){
        this.setsBoardName("Free Board");
    }
    public void setsBoardName(String sBoardName){
        this.sBoardName = sBoardName;
    }
    public String getsBoardName(){
        return sBoardName;
    }
    public void doWrite() {
        System.out.println(this.getsBoardName() + "Write Down!!");
    }
}
```


* @PostConstruct 는 생성자가 실행된 후 바로 실행된다. 즉, 객체가 생성될때 같이 실행된다.

* @PreDestory는 객체가 파괴되기 전에 실행된다. 즉, 객체가 사라지기 직전에 실행된다.
<br>
    **(자주 사용되지 않는다.)**

## AOP

* pom.xml에 AOP 관련 라이브러리를 추가 한다.

**pom.xml**
```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-aop -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>4.0.7.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.8.11</version>
    </dependency>
```

* 위의 추가는 [전에 포스팅](https://kwakkwakkwak.github.io/java/spring/2017/10/01/Spring_Setting/)에서 pom.xml 세팅했다.

### AOP - XML (part 11)

**context11.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <context:component-scan base-package="part11"/>
        <context:annotation-config></context:annotation-config>
        <aop:config>
            <aop:aspect ref="profiler">
                <aop:pointcut id="publicMethod" expression="execution(public * part11..*(..))"/>
                <aop:around pointcut-ref="publicMethod" method="trace"/>
            </aop:aspect>
        </aop:config>
        <!--<bean id="board" class="part8.BoardCafe"/>-->
        <!--<bean id="freeWrite" class="part8.FreeBoardWrite"/>-->
        <!--<bean id="qnaWrite" class="part8.QnABoardWrite"/>-->
    </beans>
```
**Profiler.java**
```java
package part11;

import org.aspectj.lang.ProceedingJoinPoint;
import org.springframework.stereotype.Component;

@Component
public class Profiler {
    public Object trace(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("profiler == Before || " + joinPoint.getSignature().toShortString());
        try {
            Object result = joinPoint.proceed();
            return result;
        }finally {
            System.out.println("profiler == After || " + joinPoint.getSignature().toShortString());
        }
    }
}

```
    
### AOP - Annotation (part 12)

**context12.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="part12"/>
    <context:annotation-config></context:annotation-config>
    <aop:aspectj-autoproxy/>
    <!--<aop:config>-->
        <!--<aop:aspect ref="profiler">-->
            <!--<aop:pointcut id="publicMethod" expression="execution(public * part11..*(..))"/>-->
            <!--<aop:around pointcut-ref="publicMethod" method="trace"/>-->
        <!--</aop:aspect>-->
    <!--</aop:config>-->
    <!--<bean id="board" class="part8.BoardCafe"/>-->
    <!--<bean id="freeWrite" class="part8.FreeBoardWrite"/>-->
    <!--<bean id="qnaWrite" class="part8.QnABoardWrite"/>-->
</beans>
```

**Profiler.java**
```java
package part12;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class Profiler {
    @Pointcut("execution(public * part12..*(..))")
    public void publicMethod(){}

    @Around("publicMethod()")
    public Object trace(ProceedingJoinPoint joinPoint) throws Throwable{
        System.out.println("profiler == Before || " + joinPoint.getSignature().toShortString());
        try {
            Object result = joinPoint.proceed();
            return result;
        }finally {
            System.out.println("profiler == After || " + joinPoint.getSignature().toShortString());
        }
    }
}
```
* @Aspect 

     AOP 사용을 선언

* @PointCut 
 
     표현식으로 part12의 모든 메소드(반환타입, 메소드명, 매개변수 모두)를 publicMethod 라는 이름의 껍데기에 선언한다.
  
* @Around

     @Around("메소드") 형태로 안에 메소드에 아래내용을 넣겠다. 라고 선언을 한다.
