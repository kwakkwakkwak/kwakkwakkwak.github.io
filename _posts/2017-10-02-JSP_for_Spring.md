---
title: "JSP for Spring "
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
date: 2017-10-02 
comments : true
---

_사용 예제는 [여기](https://github.com/kwakkwakkwak/JSP_Spring) 에서 확인 할 수 있습니다._

# MVC

* MVC는 Model View Controller 의 약자로 Application을 세가지 역할로 구분한 개발 방법론이다.

* 사용자가 Controller 사용해서 Model을 통해 데이터를 가져오고 그바탕으로 시각적인 표현을 View가 보여준다.

* WEB에서의 MVC
    1. 사이트 접속
    2. Controller가 Model 호출
    3. Model은 데이터를 제어
    4. Controller가 Model의 결과를 View 반영
    5. View가 보여짐
    
## Model
* DB

## View
* HTML/css/JS

## Controller
* JAVA(servlet)

# JSP for Spring

## XML setting 

**web.xml**
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <display-name>MySpringWeb</display-name>

    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/dispatcher-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>*.html</url-pattern>
    </servlet-mapping>

</web-app>

```
* disaptcher-servlet Mapping

**dispathcer-servlet.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.2.xsd">
    
  
<bean class = "com.study.controller.StudyController"/>
<bean class = "org.springframework.web.servlet.view.InternalResourceViewResolver" p:order="1">
	<property name = "prefix" value = "/WEB-INF/view/"/>
	<property name = "suffix" value = ".jsp"/>
</bean>
</beans>

```
* Bean 등록, 결과값에 접두사, 접미사를 붙여서 온전한 path와 확장자로 바꾼다.

## Code

**index.jsp**
```jsp
<!DOCTYPE html>

<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
    
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>

<html>
	<head>
		<meta charset="utf-8">
		<title>Welcome</title>
	</head> 
	<body>
		<c:url value="/study/showMessage.html" var="messageUrl" />
		<a href="${messageUrl}">Click to enter</a>
	
	</body>
</html>

```

* 첫회면 jstl core-lib이용 controller로 명령


**CommonUtil.java**

```java
package com.common.util;

public class CommonUtil {
    public static String getSelect(){
        String select = "<select id = 'searchdate'>"
                +"<option value = 'all' selected=''>-select-</option>"
                +"<option value = '1d'>1일</option>"
                + "<option value = '1w'>1주</option>"
                +"<option value = '1m'>1달 </option>"
                +"<option value = '6m'>6개월</option>"
                +"<option value = '1y'>1년</option>"
                +"</select>";
        return select;
    }
}
```
**StudyController.java**
```java
package com.study.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.common.util.CommonUtil;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;


@Controller
public class StudyController {

		@RequestMapping("/study/showMessage.html")
		public String showMessage(Model model, HttpServletRequest req, HttpServletResponse res) {

			model.addAttribute("message","hello! Spring");
			model.addAttribute("combo", CommonUtil.getSelect());
			return "showMessage"; //view 이름이다. 어떤 view를 보여줄지 이름을 정해줌.
		}		

	
}

```

* controller에서 명령 처리

    * @Controller : 클래스를 웹요청하는 컨트롤러로 사용할 수 있다.
    
    * @RequestMapping : ""안에 해당하는 페이지에 Mapping 한다.
    
    * model에 key-value 형태로 Attribute를 주고
    
    * return 에서 가져오는 값에 위에 dispatcher-servlet.xml의 prefix, suffix로 접두사 접미사를 붙여줘서 완전한 웹페이지 형태로 만들어준다.
    
## 실행 결과

**index.jsp**
![1](/assets/images/jspForSpring/1.png)

**showMessage.jsp**
![2](/assets/images/jspForSpring/2.png)
![3](/assets/images/jspForSpring/3.png)

    