---
title: "[Node] MongoBD 설치"
description: "Node 에서 MongoDB 설치"
categories: Node MongoDB
tags: MongoDB
date: 2017-11-19
comments : true
---

# Mongo DB

* Mongo DB는 비관계형 데이터 베이스, NoSQL 또는 Not Only SQL 이라고 한다.

* 비관계형 DB를 쓰는 이유는 성능 때문이다. 관계형 DB는 신뢰도를 높이기 위해 여러 장치들이 있고 그 때문에 성능이 떨어지게 된다. 하지만 비관계형 DB는 성능을 최우선으로 하기 떄문에 많은 트래픽을 감당할수 있다.

# 설치

1. 보통 OS X 에서 지원하지 않는 패키지를 관리하기 위한 툴이 많은데 그중에 HomeBrew 라는 패키지 관리자로 설치를 진행한다.

> /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

2. 터미널에 붙여넣어서 설치할 수 있다.

> $ brew install mongodb

3. 위의 명령어로 brew 에 mongoDB가 설치된다. brew mongoDB를 설치하면 환경변수 등록을 하지 않아도 된다.

# 설정

1. 데이터 저장 폴더 만들기

> $ sudo mkdir -p /data/db

2. 권한 부여하기

> chown $USER /data/db 

3. Mongod 활성화

> mongod

4. Mongo 실행

> mongo

## 명령어

* 버전 확인

> mongod --version

* DB 이동

> use db

* DB List

> show dbs

* DB 상태

> db.stats()

* DB shutdown

> use admin
>
> db.shutdownServer()

* DB Logout

> db.logout()







