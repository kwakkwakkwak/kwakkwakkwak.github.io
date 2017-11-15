---
title: "[Node] WebServer"
description: "Node.js로 WebServer 구현"
categories: javascript node 
tags: WebServer http 
date: 2017-11-13
comments : true
---


# http 웹서버 만들기

## 기본 Sever

* npm 기본모듈중에 `http`를 사용해서 server 를 만든다.

```javascript
var http = require('http');


// 웹 서버 객체를 만듭니다.
var server = http.createServer();

// 웹 서버를 시작하여 3000번 포트에서 대기하도록 합니다.
var port = 3000;
server.listen(port, function() {
    console.log(http)
	console.log('웹 서버가 시작되었습니다. : https://127.0.0.1:'+port);
});
```
* `require()` : `java`에서 `import`랑 비슷한 기능을 한다. 그리고 객체를 반환한다.

* `http`의 `.createServer()` 함수로 server 객체를 생성한다.

* `listen()` 함수를 이용해서 서버를 동작시킨다.

>    `listen()` 의 parameter
> 
>    1. port                : 포트번호
>    2. server              : 서버 주소
>    3. Request clients     : 접속자 제한
>    4. callback            : 콜백함수

## 클라이언트 이벤트 처리

```javascript
// 클라이언트 연결 이벤트 처리
server.on('connection', function(socket) {
	console.log('클라이언트가 접속했습니다. : %s, %d', socket.remoteAddress, socket.remotePort);
});

// 클라이언트 요청 이벤트 처리
server.on('request', function(req, res) {
	console.log('클라이언트 요청이 들어왔습니다.');
	console.dir(req);
});

// 서버 종료 이벤트 처리
server.on('close', function() {
	console.log('서버가 종료됩니다.');
});
```
### server.on

 * on 함수는 `~할때` Callback 함수를 실행한다.
 
    * `connection` : 연결되었을때 // callback(socket){} : parameter 로 socket 정보가 들어감
    
    * `request` : 요청이 들어왔을때 // callback(req,res){} : parameter 로 request, response 가 들어감
    
    * `close` : 서바가 종료되었을때 // callback() {} : parameter 없음
    
```javascript
server.on('request', function(req, res) {
	console.log('클라이언트 요청이 들어왔습니다.');
	
	res.writeHead(200, {"Content-Type": "text/html; charset=utf-8"});
	res.write("<!DOCTYPE html>");
	res.write("<html>");
	res.write("  <head>");
	res.write("    <title>응답 페이지</title>");
	res.write("  </head>");
	res.write("  <body>");
	res.write("    <h1>노드제이에스로부터의 응답 페이지</h1>");
	res.write("  </body>");
	res.write("</html>");
	res.end();
	  
});
```
## response 으로 화면 보여주기

* 위에 처럼 `res`(response)에 html 형식을 써서 보낼 수 있음 

    * `java`에서 `servlet` 과 같음


## 요청하기

### GET 방식
```javascript

var http = require('http');

var options = {
    host: 'www.google.com',
    port: 80,
    path: '/'
};

var req = http.get(options, function(res) {
    // 응답 처리
    var resData = '';
    res.on('data', function(chunk) {
    	resData += chunk;
    });
    
    res.on('end', function() {
	    console.log(resData);
	});
});

req.on('error', function(err) {
    console.log("에러 발생 : " + err.message);
});

````
![1](/assets/images/[node]webserver/1.png)


### POST 방식

```javascript


var http = require('http');

var opts = {
    host: 'www.google.com',
    port: 80,
    method: 'POST',
    path: '/',
    headers: {}
};


var resData = '';
var req = http.request(opts, function(res) {
	// 응답 처리
	res.on('data', function(chunk) {
		resData += chunk;
	});
	
	res.on('end', function() {
		console.log(resData);
	});
});

opts.headers['Content-Type'] = 'application/x-www-form-urlencoded';
req.data = "q=actor";
opts.headers['Content-Length'] = req.data.length;

req.on('error', function(err) {
    console.log("에러 발생 : " + err.message);
});

// 요청 전송
req.write(req.data);
req.end();

```
![2](/assets/images/[node]webserver/2.png)


