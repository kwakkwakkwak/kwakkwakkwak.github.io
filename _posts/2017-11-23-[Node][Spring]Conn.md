---
title: "[Node] [Spring] server 연동"
description: "Node express 서버와 jsp 서버에서 data 통신을 한다."
categories: Node Spring
tags: Node Spring jsp express connection
date: 2017-11-23
comments : true
---

* 사용예제는 [여기](https://github.com/kwakkwakkwak/Node-Tomcat) 에서 받아 보실 수 있습니다.

# Node

```javascript

let http = require('http');

let express = require('express');

let bodyParser = require('body-parser');

app = express();

let inputData = { data1 : 'node to tomcat testdata1', data2 : 'node to tomcat testdata2'};
// 전달하고자 하는 데이터 생성

let opts = {
    host: '127.0.0.1',
    port: 8080,
    method: 'POST',
    path: '/start',
    headers: {'Content-type': 'application/json'},
    body: inputData
};


// 포트 81 에서는 톰캣 서버가 대기하고 있다.
// 스프링 컨트롤러에서 '/start' URI 에 매핑하는 메소드를 두었다.
// 전달 방식은 json 형태로 하였다.
let resData = '';
let req = http.request(opts, function(res) {

    res.on('end', function() {
        console.log(resData);
    });
});

opts.headers['Content-Type'] = 'application/x-www-form-urlencoded';
req.data = opts ;
opts.headers['Content-Length'] = req.data.length;

req.on('error', function(err) {
    console.log("에러 발생 : " + err.message);
});


app.use(bodyParser.urlencoded({ extended: false}));

app.use(bodyParser.json());

app.use(function (req,res,next) {
    //console.log("ㅊㅊㅊㅊ");

    res.writeHead('200',{'Content-Type':'text/html;charset=utf8'});
    res.end('<h1>sssss</h1>');
    console.log(req.body);

});

// 요청 전송
req.write(JSON.stringify(req.data.body));

req.end();

http.createServer(app).listen(1516, function(){
    console.log('Server is running...');
});

```

* express 서버를 키면 자동으로 Tomcat 서버에 JSON 데이터를 전송한다.

# Spring


**Controller.java**

```java

package controller;

import org.json.simple.JSONObject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;

import java.io.*;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.net.URLEncoder;
import java.text.DateFormat;
import java.util.Date;
import java.util.Locale;
import java.util.Scanner;

@Controller
public class HomeController {

    private static final Logger logger = LoggerFactory.getLogger(HomeController.class);

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

    @RequestMapping(value = "/start", method = RequestMethod.POST, consumes = "application/json")
    @ResponseBody
    public String startApp(@RequestBody String body) {
        System.out.println(body);
        logger.info(body);
        return "/";
    }

    @RequestMapping(value = "/doA", method = RequestMethod.GET)
    public String doA(Locale locale, Model model){
        JSONObject cred = new JSONObject();
        JSONObject auth=new JSONObject();
        JSONObject parent=new JSONObject();
        cred.put("username","adm");
        cred.put("password", "pwd");
        auth.put("tenantName", "adm");
        auth.put("passwordCredentials", cred);
        parent.put("auth", auth);

        URLConn conn = new URLConn("http://127.0.0.1",1516);
        conn.urlPost(parent);

        return "index";
    }
}

```

**URLCOnn.java**

```java
package controller;

import org.json.simple.JSONObject;

import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStreamWriter;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.util.Scanner;

public class URLConn {
    URLConnection conn;

    public URLConn(String urlpath,int port){
        try {
            URL url = new URL(urlpath+":"+port);
            conn = url.openConnection();
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void urlPost(JSONObject jsonObject) {
        conn.setDoOutput(true);
        conn.setUseCaches(false);
        conn.setRequestProperty("Content-Type", "application/json; charset=UTF-8");
        try {
            OutputStreamWriter wr= new OutputStreamWriter(conn.getOutputStream());
            wr.write(jsonObject.toString());
            wr.flush();

            InputStream is = conn.getInputStream();
            Scanner sc = new Scanner(is);
            int line =1;
            while (sc.hasNext()){
                String str = sc.nextLine() ;
                System.out.println((line++) + ":" + str);
            }
            sc.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

* POST 형식으로 모두 주고 받는다. URLConn 에서 JSON 파일 형식으로 보내고

* `startApp()` 데이터 요청을 대기하고, `getData()` 에서는 Node 로 Json 데이터를 보낸다.

* Node 에서도 받을 데이터를 기다리면 대기하고있다.

