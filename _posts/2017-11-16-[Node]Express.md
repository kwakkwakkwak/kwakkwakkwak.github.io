---
title: "[Node] Express"
description: "Express 로 웹서버 만들기"
categories: Node Express
tags: Express 
date: 2017-11-16
comments : true
---

# Express 웹서버 만들기

* node 웹서버 Framework

## 기본 Express 웹 서버

```javascript
// Express 기본 모듈 불러오기
var express = require('express')
  , http = require('http');
 
// 익스프레스 객체 생성
var app = express();

// 기본 포트를 app 객체에 속성으로 설정
app.set('port', process.env.PORT || 3000);

// Express 서버 시작
http.createServer(app).listen(app.get('port'), function(){
  console.log('익스프레스 서버를 시작했습니다 : ' + app.get('port'));
});
``` 

* `express` 객체 생성하고, `.set('속성', 값)` 속성지정

* http 에서 `express`를 이용해서 서버를 만들고 `.listen()`로 서버를 올립니다.

## 미들웨어 사용하기 

```javascript
// 직접 미들웨어 객체를 만들어 설정
app.use(function(req, res, next) {
	console.log('첫번째 미들웨어에서 요청을 처리함.');
	
	res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
	res.end('<h1>Express 서버에서 응답한 결과입니다.</h1>');

});
```

* 미들웨어를 직접만들어서 사용하는 예제

* callback function 의 parameter 값이 request, response, next 를 받음 response 로 화면을 만들어 줄 수 있다.

## 미들웨어의 중복

```javascript
// 첫번째 미들웨어에서 다음 미들웨어로 넘김
app.use(function(req, res, next) {
	console.log('첫번째 미들웨어에서 요청을 처리함.');
	
	req.user = 'mike';

	next();
});

// 두번째 미들웨어에서 응답 전송
app.use('/', function(req, res, next) {
	console.log('두번째 미들웨어에서 요청을 처리함.');
	
	res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
	res.end('<h1>Express 서버에서 ' + req.user + '가 응답한 결과입니다.</h1>');

});
```

* 미들웨어는 여러개 쓸 수 있다.

* next 함수로 다음 미들웨어로 값을 넘길수 있다. 

* parameter 로 경로를 지정할 수 있다.

```javascript

res.send({name:'소녀시대', age:20});
	
res.redirect('http://google.co.kr');
	
```

* 미들웨어에서 응답할때, `.send()`로 json 으로 데이터 전송

* 미들웨어에서 응답할때, `.redirect()`로 이동

## 기본 미들웨어


### serve-static

* 특정 폴더의 파일들을 특정 패스로 접근 할 수 있도록 만들어준다.

> `use(static(path.join(__dirname,'public')))`
>
> `path`를 이용해서 `static_path`로 만든다.
>
>  형태 public 폴더를 기본 폴더로 사용 할 수 있다.

### body-parser

* `POST`요청했을때 요청파라미터를 확인 할 수 있다.

> `.use(bodyParser.urlencoded({ extended: false }))`
>
>  body-parser를 이용해 POST오는 데이터 파싱
>
> `.use(bodyParser.json())`
>
>  bodyParser를 이용해서 json 파싱한다.
 
## 라우터

### 라우터 사용하기 

```javascript
var router = express.Router();

// 라우팅 함수 등록
router.route('/process/login').post(function(req, res) {
	console.log('/process/login 처리함.');

	var paramId = req.body.id || req.query.id;
	var paramPassword = req.body.password || req.query.password;
	
	res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
	res.write('<h1>Express 서버에서 응답한 결과입니다.</h1>');
	res.write('<div><p>Param id : ' + paramId + '</p></div>');
	res.write('<div><p>Param password : ' + paramPassword + '</p></div>');
	res.write("<br><br><a href='/public/login2.html'>로그인 페이지로 돌아가기</a>");
	res.end();
});

// 라우터 객체를 app 객체에 등록
app.use('/', router);
``` 
 * 라우팅 함수를 등록하고
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>로그인 테스트</title>
	</head>
<body>
	<h1>로그인</h1>
	<br>
	<form method="post" action="/process/login">
		<table>
			<tr>
				<td><label>아이디</label></td>
				<td><input type="text" name="id" /></td>
			</tr>
			<tr>
				<td><label>비밀번호</label></td>
				<td><input type="password" name="password" /></td>
			</tr>
		</table>
		<input type="submit" value="전송" name=""/>
	</form>
</body>
</html>
```
* 매칭되는 action 을 만들어 준다.

### 라우터 요청패스에 파라미터 값 명시해주기

```javascript

router.route('/process/login/:name').post(function(req, res) {
	console.log('/process/login/:name 처리함.');

    var paramName = req.params.name;
    
	var paramId = req.body.id || req.query.id;
	var paramPassword = req.body.password || req.query.password;
	
	res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
	res.write('<h1>Express 서버에서 응답한 결과입니다.</h1>');
    res.write('<div><p>Param name : ' + paramName + '</p></div>');
	res.write('<div><p>Param id : ' + paramId + '</p></div>');
	res.write('<div><p>Param password : ' + paramPassword + '</p></div>');
	res.write("<br><br><a href='/public/login3.html'>로그인 페이지로 돌아가기</a>");
	res.end();
});

```








