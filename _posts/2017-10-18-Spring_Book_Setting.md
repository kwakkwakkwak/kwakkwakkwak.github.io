---
title: "Spring_Book_Setting"
description: "JSP for Spring Framework & MVC Pattern"
categories: 
 JSP 
 Spring
 MVC
tags: 
 JSP
 Spring
 MVC
 intellij
 setting
date: 2017-10-11 
comments : true
---

_사용 예제는 [여기](https://github.com/kwakkwakkwak/spring_book_setting) 에서 확인 할 수 있습니다._

# Maven project 생성 

 ![2](/assets/images/SpringBookSetting/1.png)
 
 * Maven 프로젝트를 생성합니다.
 
 * 여기서 archetpye으로 쉽게 어느정도 구조화된 프로젝트를 만들수 있지만
 
 원하는 구조가 없으므로 기본 maven프로젝트로 만듭니다.
 
 ![3](/assets/images/SpringBookSetting/2.png)
 
 * 적당한 GroupId ArtifactId를 설정하고,
 
 ![2](/assets/images/SpringBookSetting/3.png)
 
 * 프로젝트 이름을 넣어주고 마칩니다.
 
 ![3](/assets/images/SpringBookSetting/4.png)
 * 그럼 정말 깨끗한 Maven Project가 완성됩니다.
 
 
 # Spring Framework 추가
 
 * 프로젝트에서 오른쪽 클릭으로 열면,
 
 ![2](/assets/images/SpringBookSetting/5.png)
 
 * Add Framework Support를 클릭하고
  
 ![3](/assets/images/SpringBookSetting/6.png)
 
 * JSP프로젝트를 할 것이므로, WebServices를 선택,
 
 ![2](/assets/images/SpringBookSetting/7.png)
 
 * Srping mvc 패턴으로 만들 것이므로 Spring mvc를 선택,      
    후에 Ok를 눌러주면 다운로딩이 완료됩니다.
 
 * framework를 추가하긴 했지만 dependency가 추가되지 않은 모습입니다,   
    그래서 xml에 우리가 필요한 것을 추가할 것입니다.
 
 **pom.xml**
 
 ```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.zerok</groupId>
    <artifactId>web</artifactId>
    <name>ex002</name>
    <packaging>war</packaging>
    <version>1.0.0-BUILD-SNAPSHOT</version>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java-version>1.8</java-version>
        <org.springframework-version>4.3.8.RELEASE</org.springframework-version>
        <org.aspectj-version>1.6.10</org.aspectj-version>
        <org.slf4j-version>1.6.6</org.slf4j-version>
    </properties>
    <dependencies>
        <!-- Spring -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${org.springframework-version}</version>
            <exclusions>
                <!-- Exclude Commons Logging in favor of SLF4j -->
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>

        <!-- AspectJ -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>${org.aspectj-version}</version>
        </dependency>

        <!-- Logging -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${org.slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>${org.slf4j-version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${org.slf4j-version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.15</version>
            <exclusions>
                <exclusion>
                    <groupId>javax.mail</groupId>
                    <artifactId>mail</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>javax.jms</groupId>
                    <artifactId>jms</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.sun.jdmk</groupId>
                    <artifactId>jmxtools</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>com.sun.jmx</groupId>
                    <artifactId>jmxri</artifactId>
                </exclusion>
            </exclusions>
            <scope>runtime</scope>
        </dependency>

        <!-- @Inject -->
        <dependency>
            <groupId>javax.inject</groupId>
            <artifactId>javax.inject</artifactId>
            <version>1</version>
        </dependency>

        <!-- Servlet -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>

        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!-- Test -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java
              MYSQL Mybats-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.41</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.1</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
        <!-- MYSQL - jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <!--&lt;!&ndash;jackson:json&ndash;&gt;-->
        <!--<dependency>-->
            <!--<groupId>com.fasterxml.jackson.core</groupId>-->
            <!--<artifactId>jackson-databind</artifactId>-->
            <!--<version>2.8.4</version>-->
        <!--</dependency>-->
        <!-- https://mvnrepository.com/artifact/com.googlecode.log4jdbc/log4jdbc -->
        <!-- jdbc-mybatis Log4jdbc-->
        <dependency>
            <groupId>org.bgee.log4jdbc-log4j2</groupId>
            <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
            <version>1.16</version>
        </dependency>

    </dependencies>
    <build>

        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <source>${java-version}</source>
                    <target>${java-version}</target>
                    <compilerArgument>-Xlint:all</compilerArgument>
                    <showWarnings>true</showWarnings>
                    <showDeprecation>true</showDeprecation>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <configuration>
                    <mainClass>org.test.int1.Main</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

```

 
 ![3](/assets/images/SpringBookSetting/8.png)
 ![2](/assets/images/SpringBookSetting/9.png)
 ![3](/assets/images/SpringBookSetting/10.png)
 ![2](/assets/images/SpringBookSetting/11.png)
 ![3](/assets/images/SpringBookSetting/12.png)
 ![2](/assets/images/SpringBookSetting/13.png)
 ![3](/assets/images/SpringBookSetting/14.png)
 ![2](/assets/images/SpringBookSetting/15.png)
 ![3](/assets/images/SpringBookSetting/16.png)
 ![2](/assets/images/SpringBookSetting/17.png)
 ![3](/assets/images/SpringBookSetting/18.png)
 ![2](/assets/images/SpringBookSetting/19.png)
