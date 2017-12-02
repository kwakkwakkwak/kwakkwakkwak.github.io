---
title: "[Spring] DB Setting"
description: "Mysql Workbench 에서 설정과 Spring 에서 Test"
categories: Mysql Workbench Spring
tags: mysql workbench adduser 
date: 2017-12-02
comments : true
---

* Spring 프로젝트를 진행하는데 지난 번에 배웟던 내용이 기억나지 않아서 다시 해본다.

 
# 계정 생성

 * 새로운 계정을 생성해서 그 `Schema`에만 권한을 줘서 다른 `Schema`에 접근 못하도록 만들것입니다.
 
1. `Users and Privileges`에 들어가서 

    ![adduser](/assets/images/DBsetting/add0.png)

2. 왼쪽아래 `Add Account`로 새로운 계정을 생성합니다.

    ![adduser](/assets/images/DBsetting/add1.png)

3. 계정 정보를 등록합니다.

    ![adduser](/assets/images/DBsetting/add2.png)


# DB 생성

* 다음 DB(Schema) 를 생성하고 생성한 계정에 권한을 부여 할 것입니다. 앞서 `Schema`을 만듭니다.

1. `Create Schema` 를 선택하고
 
    ![Create Schema](/assets/images/DBsetting/createSchema.png)

2. 적당한 이름을 주고 `Schema` 를 생성합니다.

    ![Create Schema](/assets/images/DBsetting/createSchema2.png)


# 권한 생성

* 전에 계정에 `Schema`에 대한 권한을 추가합니다. tj 계정에 tj 테이블의 권한을 추가합니다.

1. User 로 다시 가서 `Schema Privileges` 탭에 `Add Entity`를 누르고

    ![permission](/assets/images/DBsetting/permission2.png)

3. `Selected schea` 에서 원하는 `Schema` 를 선택합니다.

    ![permission](/assets/images/DBsetting/permission3.png)

4. 아래쪽에 권한을 추가하는 탭이 활성화 되고 `Seletct "All"`을 선택해서 모든 권한을 부여합니다.

    ![permission](/assets/images/DBsetting/permission4.png)

5. 모든 권한을 선택하고 `Apply` 하면 된다. 

    ![permission](/assets/images/DBsetting/permission5.png)

# Spring Test

## DataSource Test

* Spring 에서 mysql 로 접속 하는 방법

1. mvn dependency 추가

**pom.xml**

```xml

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
        
```

2. `applicationContext.xml`에 Mysql 주소, username, password 추가

**applicationContext.xml**

```xml
<bean id="dataSource"
          class="org.springframework.jdbc.datasource.DriverManagerDataSource">

        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>

        <property name="url"
                  value="jdbc:mysql://127.0.0.1:3306/tj?useSSL=false"></property>

        <property name="username" value="th"></property>
        <property name="password" value="비밀번호"></property>
    </bean>

```


3. java로 데이터 소스 연결 되는지 확인

**DataSourceTest.jaa**

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.inject.Inject;
import javax.sql.DataSource;
import java.sql.Connection;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"file:src/main/webapp/WEB-INF/**/applicationContext.xml"})
public class DataSourceTest {

    @Inject
    private DataSource ds;

    @Test
    public void DataSourceTest() throws Exception{
        try (Connection con = ds.getConnection()) {

            System.out.println(con);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

}
```

## Mybatis Test

1. `mybatis-config.xml` 생성

    **mybatis-config.xml**

```xml

<?xml version="1.0" encoding="UTF-8"?>
		<!DOCTYPE configuration
				PUBLIC "-//mybatis.org/DTD Config 3.0/EN"
				"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

</configuration>

```

2. `applicationContext.xml`에 sqlSessionFactory 추가, `mybatis-config.xml` 에 연결

    **applicationContext.xml**

```xml

<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="configLocation" value="classpath:/mybatis-config.xml"></property>
    </bean>


```

3. `MybatisTest.java`에서 테스트

    **MybatisTest.java**

```java

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.inject.Inject;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"file:src/main/webapp/WEB-INF/**/applicationContext.xml"})
public class MybatisTest {

    @Inject
    private SqlSessionFactory sqlFactory;

    @Test
    public void testFactory() {
        System.out.println(sqlFactory);
    }

    @Test
    public void testSession()throws Exception{
        try(SqlSession session = sqlFactory.openSession()){
            System.out.println(session);
        }catch(Exception e) {
            e.printStackTrace();
        }
    }

}


```
