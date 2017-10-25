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
comments : true
---

## @Autowired (예제 part5,6)

* 생성자와 setter 생략


## @Qualifier (예제 part7)

* Annotation을 이용한 자동 설정제한



## @Resource (part 8)

* @Autowired, @Qulifier를 @Resource 로 변경



* 기본적으로 다른 Bean을 참조해서 사용하는 경우 @Autowired 또는 @Resource를 사용한다.


 | \ | @Autowired  | @Resource  |
 | --- | --- | --- |
 | 객체선택 기준 | Type | Name |
 | 지원 |  Spring Framework | Spring Framework 2.5.* |
 | 입력  Parameter | 여러개 | 1 개 |
 
## @Component (part 9)

* `dispatcher-servlet` 파일에 < context:component-scan> 을 추가해서 간단하게 만들고 Bean에 객체를 검색해서 주입한다.

## @PostConstruct & @PreDestory (part 10)

 * `dispatcher-servlet` 파일에 < context:annotation-config/> 해야 사용가능하다.

 * @PostConstruct 는 생성자가 실행된 후 바로 실행된다. 즉, 객체가 생성될때 같이 실행된다.

 * @PreDestory는 객체가 파괴되기 전에 실행된다. 즉, 객체가 사라지기 직전에 실행된다.
 <br> **(자주 사용되지 않는다.)**

## AOP

* pom.xml에 AOP 관련 라이브러리를 추가 한다.

### AOP - XML 

**XML에서 AOP**
```xml
<aop:config>
        <aop:aspect ref="profiler">
            <aop:pointcut id="publicMethod" expression="execution(접근제한자 반환타 패키지.메소드명(매개변수))"/>
            <aop:around pointcut-ref="publicMethod" method="trace"/>
        </aop:aspect>
    </aop:config>
```

**java에서 AOP Method**
```java
public Object trace(ProceedingJoinPoint joinPoint)
```
* Method 

### AOP - Annotation 
```java
@Component
@Aspect
public class Test {
    @Pointcut("execution(접근제한자 반환타 패키지.메소드명(매개변수))")
    void publicMethod(){}; 
    @Around("publicMethod()")
    public Object MethodName(ProceedingJoinPoint pjp) throws Throwable{
       //Coding
    }
```

* @Aspect 

     AOP 사용을 선언

* @PointCut 
 
     * execution(패키지 메소드(반환타입, 메소드명, 매개변수))를 publicMethod 라는 이름의 껍데기에 선언한다.
  
* @Around

     * @Around("메소드") 형태로 안에 메소드에 아래내용을 넣겠다. 라고 선언을 한다.
     
     * @Around("execution(패키지 메소드(반환타입, 메소드명, 매개변수))") 형태로 @PointCut 를 생략 한다.
     
