---
title: "encodeURIComponent와 decodeURIComponent"
date: 2020-02-07 18:00:00 -0900
categories: URI encodeURIComponent decodeURIComponent
---

## URI와 URL은 무엇인가?
encodeURIComponent와 decodeURIComponent를 이해하기 위해서는 먼저 URI에 대하여 이해해야 한다. URI는 Uniform Resource Identifier에 약자로 통일 자원 식별자라고 생각하면 된다. 과거에는 URL을 사용하였다. URL은 Uniform Resource Locator에 약자로 통일 자원 위치라고 생각하면된다. 과거에는 URL을 많이 썼는데 현재는 URI를 사용하는 추세이다. 

## URI와 URL에 차이
URL을 사용하는 주소는 다음과 같이 표현한다.<br>
ex.) http://abc.com/user/name.pdf<br>
다음 주소는 abc.com이라는 서버에서 user라는 폴더에 접근하여 name.pdf를 가져오라는 뜻이다. 이렇게 URL은 네트워크 상에서 자원이 어디에 존재하는지 알려주는 규약이다. 웹 사이트에서 요청하는 파일이라기 보다는 구분자로 보는 것이 맞다.

URI를 사용하는 주소는 다음과 같이 표현한다.<br>
ex.) http://abc.com/user/name.pdf?showid=1<br>
다음 주소는 위에서 설명한 URL방식에서 파일을 읽어들인 뒤 특정한 요청을 하는 뜻이다(URL은 http://abc.com/user/name.pdf까지만).URI는 항상 프로토콜이 붙는다. 이는 URI가 인터넷에서 자원을 식별하는 문자열이란 뜻이다. URI는 URL보다 큰 개념이다. URL에 쿼리스트링이 어떤 것이 붙냐에 따라서 URI가 달라질 수 있다.

## URN은 또 무엇인가?
공부를 하던 중 URN이란 개념도 알게 되었고 URL과 URI와 다른점은 무엇인지 찾아보았다. URN은 Uniform Resource Name에 약자로 통일 자원 이름이라고 생각할 수 있다. 위치와 상관없이 리소스에 이름값으로 접근을 하는 방식이다. 만약 주어진 URL은 http://abc.com/user/name인데 http://abc.com/user/list/name으로 접근한다면 404를 띄울 것이다. 이를 방지하기 위하여 위치 정보와 무관하게 리소스를 찾게 해주는 방식이다.

## URI를 인코딩하는 이유
encodeURIComponent와 decodeURIComponent의 주된 목적은 주소를 인코딩하고 디코딩하기 위함이다. 그러면 왜 URI를 인코딩해야 하는 것일까? 그 이유는 URI를 전송시에 모든 문자를 온전히 전송하기 위함이다. URI는 아스키코드로 구성이 되어야한다. 따라서 URI를 전송할 때 인코딩을 통해 아스키코드로 표현할 수 없는 문자나 특수기호를 %와 16진수에 조합을 이용하여 표현한다.

## 예제
다음은 html코드이다. 사람 이름을 입력받아 서버로 전송한다.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>URIComponent</title>
</head>

<body>
    <form action="/process" method="GET">
        <table>
            <td><label>유저이름 : <input type="text" name="name"></label> </td>
        </table>
        <input type="submit">
    </form>
</body>

</html>
```

<br>
다음은 서버이다. 클라이언트에서 GET으로 받은 정보를 인코딩과 디코딩하여 출력한다.

```javascript
const express = require('express');
const http = require('http');
const static = require('serve-static');
const path = require('path');

const app = express();
const router = express.Router();

app.use('/index',static(path.join(__dirname, 'index')));
app.use('/', router);


router.route('/').get((req, res) => {
    res.redirect('/index/index.html');
});

router.route('/process').get((req, res, next) => {
    const encode = encodeURIComponent(req.query.name);
    const decode = decodeURIComponent(encode);

    res.writeHead(200,{'Content-Type':'text/html; charset=utf-8'});
    res.write(`<p>인코딩한 결과 : ${encode}</p>`);
    res.write(`<p>디코딩한 결과 : ${decode}</p>`);
    res.end();

});

http.createServer(app).listen(3000, () => {
    console.log('3000번 대기 중');
});
```

<br>
다음은 결과 화면이다.
<p align="center">
    <img align="center" src="https://github.com/hansanguk0222/hansanguk0222.github.io/blob/master/image/URI1.PNG?raw=true" alt="동작하기 전">
</p>
<p align="center">
    <img align="center" src="https://github.com/hansanguk0222/hansanguk0222.github.io/blob/master/image/URI2.PNG?raw=true" alt="동작한 후">
</p>

서버에서 인코딩한 결과를 보면 한글이 %와 함께 16진수로 표현된 것을 확인할 수 있다.

## 참고자료
[pa324](https://velog.io/@pa324/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-URI-URL-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC)
[위키피디아 URL](https://ko.wikipedia.org/wiki/URL)
[위키피디아 URI](https://ko.wikipedia.org/wiki/%ED%86%B5%ED%95%A9_%EC%9E%90%EC%9B%90_%EC%8B%9D%EB%B3%84%EC%9E%90)
[lambdaexp](https://lambdaexp.tistory.com/39)
