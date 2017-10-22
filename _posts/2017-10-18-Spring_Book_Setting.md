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
 
 * 여기서 `archetpye`으로 쉽게 어느정도 구조화된 프로젝트를 만들수 있지만
 
 원하는 구조가 없으므로 기본 `maven`프로젝트로 만듭니다.
 
 ![3](/assets/images/SpringBookSetting/2.png)
 
 * 적당한 GroupId ArtifactId를 설정하고,
 
 ![2](/assets/images/SpringBookSetting/3.png)
 
 * 프로젝트 이름을 넣어주고 마칩니다.
 
 ![3](/assets/images/SpringBookSetting/4.png)
 * 그럼 정말 깨끗한 `Maven Project`가 완성됩니다.
 
 
# Spring Framework 추가
 
 * 프로젝트에서 오른쪽 클릭으로 열면,
 
 ![2](/assets/images/SpringBookSetting/5.png)
 
 * Add Framework Support를 클릭하고
  
 ![3](/assets/images/SpringBookSetting/6.png)
 
 * JSP프로젝트를 할 것이므로, WebServices를 선택,
 
 ![2](/assets/images/SpringBookSetting/7.png)
 
 * Srping mvc 패턴으로 만들 것이므로 Spring mvc를 선택,      
    후에 Ok를 눌러주면 다운로딩이 완료됩니다.
 
 ![3](/assets/images/SpringBookSetting/8.png)
 
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
 
![3](/assets/images/SpringBookSetting/12.png)
 
 * `project property` 에 `Libraries`에 있는 파일을 삭제합니다.
 
 * 혹시모를 쓸데없는 파일이 `dependency`에 있을지 모르니 한번 깨끗하게 정리 해줍니다. 
    
 
 ![3](/assets/images/SpringBookSetting/10.png)
 
 * 후에 프로젝트의  `maven` 을 reimport 해줍니다.
 
 
 
 
 
 ![2](/assets/images/SpringBookSetting/13.png)
 * 모든 소스코드는 src내에 있어야 관리하기도 편하고 터질일이 없으므로 사진과 같이 정리를 해줍니다.
 
 * 필요한 폴더를 만들어 추가 해줍니다. (web/resources, web/WEB-INF/views )
 
 * 불필요한 xml을 정리하고 `web.xml`에서도 제거합니다. (아래 Test에서 자세히 하겠습니다.) 
 
 # Tomcat 설정
 
 ![3](/assets/images/SpringBookSetting/14.png)
 
 * `Tomcat`은 설치되어 있는 환경에서 시작합니다.
 
 * `Edit Configuration` 에서 
 
  ![2](/assets/images/SpringBookSetting/15.png)
 
 * `+`를 누르고 아래로 드래그 해보면 `TomcatServer`가 있습니다.(없으신 분들은 더아래 `times more`를 클릭하시면 나옵니다. )

 ![3](/assets/images/SpringBookSetting/16.png)
 
 * 'Local' 선택

 ![2](/assets/images/SpringBookSetting/17.png)
 
 * `Server` 정보가 나와있습니다 `Http port` 포트번호를 바꿀수 있고 `JMX port` 번호도 겹치면 안됩니다.
 
 * 혹시 Server를 2개이상 쓰실분들이라면 둘다 포트번호를 바꿔서 만들어야 나중에 서버 충돌이 나는것을 방지할 수 있습니다.
 
 * 왼쪽 아래쪽에 Fix 버튼이 나와있는 곳을 누르고 첫번째 war파일을 선택해줍니다.

 ![3](/assets/images/SpringBookSetting/18.png)
 
 * 위에 처럼 war 파일이 만들어 지고 이제 서버를 실행시킬 수 있습니다. 
 
 ![2](/assets/images/SpringBookSetting/19.png)
 
 *이제 서버를 실행 시킬수 있습니다.
 
 
 # Test 
 
 * 이제 개발환경이 제대로 설치가 되었는지 테스트를 할차례 입니다.
 
 **web.xml**
 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
          http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
          version="3.1">
     <context-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>/WEB-INF/applicationContext.xml</param-value>
     </context-param>
     <listener>
         <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
     </listener>
 
     <servlet>
         <servlet-name>dispatcher</servlet-name>
         <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
         <init-param>
             <param-name>contextConfigLocation</param-name>
             <param-value>WEB-INF/dispatcher-servlet.xml</param-value>
         </init-param>
         <load-on-startup>1</load-on-startup>
     </servlet>
     <servlet-mapping>
         <servlet-name>dispatcher</servlet-name>
         <url-pattern>/</url-pattern>
     </servlet-mapping>
 
 </web-app>
 ```
 
 * web.xml에서 불필요한 코드를 제거하고 `dispatcher-servlet`을 직접적으로 연결해줍니다.
 
 **dispqtcher-servelt.xml**
 
 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:beans="http://www.springframework.org/schema/mvc"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
        
        <!-- MVC Annotation Setting-->
     <beans:annotation-driven/>
 
     <!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
     <beans:resources mapping="/resources/**" location="/resources/" />
 
     <!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
     <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
         <property name="prefix" value="/WEB-INF/views/" />
         <property name="suffix" value=".jsp" />
     </bean>
 
    <!--Scan Beans-->
     <context:component-scan base-package="controller" />
 </beans>
```

* `dispatcher-servlet`에서
    * resource 를 가져올 곳
    * `MVC의 Annotation`을 위한 `annotation-driven`
    * 접두사(`prefix`) 접미사(`suffix`)를 이용해서 경로 설정위한 bean
    * `component-scan`을 통해서 `base-package` 안에서 `@Controller`, `@Service`, `@Component`, `@Repository` 와 같은 `Annotation` 설정은 `Bean`으로 등록합니다.
 
**index.jsp**
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
  <title>Test</title>
</head>
<body>
${serverTime}
</body>
</html>
```
 *  `index.jsp`에 EL객체 ${serverTime}을 추가하고
 
 **IndexController.java**
 
 ```java
 package controller;
 
 import java.text.DateFormat;
 import java.util.Date;
 import java.util.Locale;
 
 import org.slf4j.Logger;
 import org.slf4j.LoggerFactory;
 import org.springframework.stereotype.Controller;
 import org.springframework.ui.Model;
 import org.springframework.web.bind.annotation.RequestMapping;
 import org.springframework.web.bind.annotation.RequestMethod;
 
 /**
  * Handles requests for the application home page.
  */
 @Controller
 public class IndexController {
 
     private static final Logger logger = LoggerFactory.getLogger(IndexController.class);
 
     /**
      * Simply selects the home view to render by returning its name.
      */
     @RequestMapping(value = "/", method = RequestMethod.GET)
     public String home(Locale locale, Model model) {
         logger.info("Welcome home! The client locale is {}.", locale);
 
         Date date = new Date();
         DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
 
         String formattedDate = dateFormat.format(date);
 
         model.addAttribute("serverTime", formattedDate );
 
         return "index";
     }
 
 }

```

* 위와 같이 IndexController를 만들어 줍니다.

* 파일 위치는 위에서 접두사 접미사를 붙여서 만들어 주고 `component-scan`에서 찾아올수 있는 `src/java/controller` 안에 만들어 줍니다.

* 후에 서버를 실해시키면

![2](/assets/images/SpringBookSetting/20.png)

 * 이런 결과를 얻어 볼수 있습니다.
 
 **_`끗`_**
