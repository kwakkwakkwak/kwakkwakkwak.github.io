---
title: "[Node] MongoDB 연동"
description: "Express MongoDB 연동"
categories: Node MongoDB
tags: Mongo DB NoSQL Express Node
date: 2017-11-20
comments : true
---

# NPM 에서 모듈 설치

* 명령어

> npm install mongodb

# mongodb module 사용 

* 데이터 베이스의 연결 

```javascript

var MongoClient = require('mongodb').MongoClient;

// 데이터베이스 객체를 위한 변수 선언
var database;

//데이터베이스에 연결
function connectDB() {
	// 데이터베이스 연결 정보
	var databaseUrl = 'mongodb://localhost:27017/local';
	
	// 데이터베이스 연결
	MongoClient.connect(databaseUrl, function(err, db) {
		if (err) throw err;
		
		console.log('데이터베이스에 연결되었습니다. : ' + databaseUrl);
		
		// database 변수에 할당
		database = db;
	});
}
``` 

* 사용자 인증 


```javascript

// 사용자를 인증하는 함수
var authUser = function(database, id, password, callback) {
	console.log('authUser 호출됨 : ' + id + ', ' + password);
	
    // users 컬렉션 참조
	var users = database.collection('users');

    // 아이디와 비밀번호를 이용해 검색
	users.find({"id":id, "password":password}).toArray(function(err, docs) {
		if (err) { // 에러 발생 시 콜백 함수를 호출하면서 에러 객체 전달
			callback(err, null);
			return;
		}
		
	    if (docs.length > 0) {  // 조회한 레코드가 있는 경우 콜백 함수를 호출하면서 조회 결과 전달
	    	console.log('아이디 [%s], 패스워드 [%s] 가 일치하는 사용자 찾음.', id, password);
	    	callback(null, docs);
	    } else {  // 조회한 레코드가 없는 경우 콜백 함수를 호출하면서 null, null 전달
	    	console.log("일치하는 사용자를 찾지 못함.");
	    	callback(null, null);
	    }
	});
}

```

* 간단하게 mongodb 에서 사용자데이터를 찾는 함수를 등록한다. 

* 도메인 등록 부터 모든 작업을 express 에서 할수 있다. 




