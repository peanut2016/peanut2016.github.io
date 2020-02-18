---
title: "queryString"
date: 2020-02-09 21:00:00 -0900
categories: queryString GET
---

## 쿼리란?
* 웹 브라우저에서 경우에 따라 url주소에 필요한 파라미터를 넘기는 데 이것을 쿼리라 한다. 
* ?뒤에 붙은 값이다.
* 파라미터 = 값에 형태로 주어진다.
* 여러개가 올 경우 &연산자로 연결된다.
* GET으로 넘길 때 사용한다.

## 예제
다음은 폼에서 쿼리를 넘기는 html 파일이다.
```html
<!DOCTYPE html>

<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>queryString</title>
</head>

<body>
    <form action="/process" method="GET">
        <label>국가 : <input type="text" name="country"></label><br>
        <label>나이 : <input type="text" name="age"></label><br>
        <label>취미 : <input type="text" name="hobby"></label><br>
        <label>성별 : <input type="text" name="gender"></label><br>
        <label>키 : <input type="text" name="tall"></label><br>
        <input type="submit">
    </form>
</body>

</html>
```

<br>

다음은 쿼리를 분석하는 서버이다.
```javascript
const express = require('express');
const static = require('serve-static');
const path = require('path');
const queryString = require('querystring');
const http = require('http');

const app = express();
const router = express.Router();

app.use('/index',static(path.join(__dirname, 'index')));
app.use('/', router);

router.route('/').get((req, res) => {
    res.redirect('/index/index.html');
});

router.route('/process').get((req, res) => {
    //1. url을 이용하여 분리하기
    const qStr1 = req.url.split('?').pop(); //url에 붙은 쿼리를 잘라내기 위하여 먼저 url을 얻어온다.
    const res1 = queryString.stringify(queryString.parse(qStr1));
    const res2 = queryString.parse(qStr1, '&', '=', {maxKeys : 2});
    const res3 = queryString.parse(qStr1, '&', '=', {maxKeys : 3});
    const res4 = queryString.stringify(res2);
    const res5 = queryString.stringify(res3, '; ', '->');

    //2. req객체에서 query속성을 이용하여 분리하기
    const qStr2 = req.query;
    const country = qStr2.country;
    const age = qStr2.age;
    const hobby = qStr2.hobby;
    const gender = qStr2.gender;
    const tall = qStr2.tall;

    res.writeHead(200, {'Content-Type':'text/html; charset=utf-8'});
    res.write(`<h2>querystring 모듈을 활용</h2>`);
    res.write(`<p>모든 쿼리 : ${res1}</p>`);
    res.write(`<p>쿼리 2개 추출 : ${res4}</p>`);
    res.write(`<p>쿼리 3개 추출하고 각 쿼리를 ;로 구분하고 =를 ->로 대체: ${res5}</p>`);
    res.write('<br>');

    res.write(`<h2>req 객체를 활용</h2>`);
    res.write(`<p>country : ${country}</p>`);
    res.write(`<p>age : ${age}</p>`);
    res.write(`<p>hobby : ${hobby}</p>`);
    res.write(`<p>gender : ${gender}</p>`);
    res.write(`<p>tall : ${tall}</p>`);
    
    res.end();
});

http.createServer(app).listen(3000, () => {
    console.log(`3000번 대기중`);
});
```

다음은 결과이다
<p align="center">
    <img align="center" src="https://github.com/hansanguk0222/hansanguk0222.github.io/blob/master/image/queryString1.PNG?raw=true" alt="html폼">
</p>

<p align="center">
    <img align="center" src="https://github.com/hansanguk0222/hansanguk0222.github.io/blob/master/image/queryString2.PNG?raw=true" alt="서버에서 보이는 쿼리">
</p>

결과를 보면 stringfy로 문자열로 변환 시 url에 표현이 허용된 특수 문자나 글자 이외에는 %와 16진수로 표현 된다는 것을 알 수 있다.

## 참고자료
[kykevin](https://velog.io/@kykevin/aaa)
[생활코딩](https://www.opentutorials.org/module/938/7369)
