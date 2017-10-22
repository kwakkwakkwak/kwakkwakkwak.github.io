---
title: "Spring setting intellij"
description: "intellij에서 Spring Setting"
categories: 
 java 
 Spring
tags: 
 java
 Spring
 Setting
 intellij
date: 2017-10-01 
comments : true
---

# intellij에서 Spring Setting

## maven을 포함한 프로젝트 생성

* intellj Creare New Project를 클릭하고,
![1](/assets/images/springSetting/1.png)

* maven 탭에서 아래와 같이 Create from archetype을 체크 하고, webapp을 선택하고,
![2](/assets/images/springSetting/2.png)

* 적당한 groupid와 artifactid를 주고,
![3](/assets/images/springSetting/3.png)

* maven 버전을 확인하고,
![4](/assets/images/springSetting/4.png)

* 프로젝트 이름과 경로를 확인하고,
![5](/assets/images/springSetting/5.png)

* 프로젝트가 완성되었습니다.
![6](/assets/images/springSetting/6.png)

## MVC 패턴, Spring framework 추가 

* project root에 오른쪽 클릭하고 add Framework support...을 열고
![7](/assets/images/springSetting/7.png)
* Spring MVC를 체크하고 ok하면 spring 다운을 시작합니다.
![8](/assets/images/springSetting/8.png)
* 모두 완료되면 pom.xml을 열어봅니다.
![9](/assets/images/springSetting/9.png)

* 그리고 xml을 변경해줍니다. <주의!!> `PROJECT_NAME` 을 잘설정 해줘야 합니다.

```xml
 <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>org.springframework.samples.service.service</groupId>
   <artifactId>PROJECT_NAME</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   <packaging>war</packaging>
 
   <properties>
 
     <!-- Generic properties -->
     <java.version>1.8</java.version>
     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
     <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
 
     <!-- Spring -->
     <spring-framework.version>4.0.7.RELEASE</spring-framework.version>
 
   </properties>
 
 
 <!-- 여러 라이브러리 추가는 dependencies안에서 행한다.-->
   <dependencies>
 
     <!-- Spring MVC -->
     <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>${spring-framework.version}</version>
     </dependency>
 
 
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
 
 
   </dependencies>
 <!-- 자바 빌드를 하기 위한 xml코드-->
   <build>
     <plugins>
       <plugin>
         <artifactId>maven-compiler-plugin</artifactId>
         <configuration>
           <source>${java.version}</source>
           <target>${java.version}</target>
         </configuration>
       </plugin>
     </plugins>
   </build>
 </project>
```

* xml을 적용한 후에는 아래 그림과 같이 reiomport 를 해주세요.(파일이 꼬일수 있어요) 
![1](/assets/images/springSetting/10.png)


기본적인 설정은 끝~

