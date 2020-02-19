---
title: "fileStream1"
date: 2020-02-19 21:00:00 -0900
categories: fileStream 파일스트림 stream 스트림
---

## 스트림이란?
사용자가 컴퓨터의 입력 장치를 사용하여 명령을 내렸다고 가정을 하자. 그러면 컴퓨터로 신호가 들어가 하드웨어적인 부분을 처리하여 입력이 되고 출력장치를 통하여 결과가 나올 것이다. 이 과정에서 우리를 하드웨어 부분까지 신경을 쓰지 않도록 도와주는 것이 **스트림**이다. 

스트림은 데이터, 패킷, 비트 등의 일련의 연속성을 갖는 흐름이다. 작은 데이터 조각들이 모여 하나의 줄기를 이루어 전송되는 데이터 열이라 생각하면 된다.

## 스트림을 사용하는 이유
스트림이 등장하게 된 이유는 물리 디스크상의 파일, 장치들을 통일된 방식으로 다루기 위해서이다. 스트림은 데이터가 어디서 나왔고 어디로 가는지 신경쓰지 않아도 되기 때문에 어떤 장치나 프로세스, 파일들과 연결될 수 있어서 개발자에게 많은 이점을 준다. 또한 많은 양의 데이터를 읽어올 때 하나의 chunk단위로 데이터를 가져와 유용하다.

## 스트림으로 파일을 작성하기
다음은 스트림으로 파일을 간단하게 작성한 예제이다.

```javascript
const fs = require('fs');
const file = fs.createWriteStream('./text.txt');

for(let i = 0; i < 1000000; i++) {
    file.write(`I love apple, pineapple, melon\n`)
}

file.end();
```
다음 결과로 100만 줄에 텍스트 파일이 생긴다.

## 파일을 스트림으로 나누어 읽기 vs 파일 전체를 한번에 읽기
위에서 만들었던 파일을 서버를 이용하여 읽어오자 <br><br>

다음은 파일 전체를 한 번에 읽어오는 방법이다.
```javascript
const fs = require('fs');
const express = require('express');
const http = require('http');

const app = express();
const router = express.Router();

app.use('/', router);

router.route('/').get((req, res) => {
    fs.readFile('./text.txt',(err, data) => {
        res.end(data);
    })
});

http.createServer(app).listen(5000, () => {
    console.log('5000번 대기 중');
});
```
<br>

다음은 파일을 스트림으로 나누어 읽어오는 방법이다.
```javascript
const fs = require('fs');
const express = require('express');
const http = require('http');

const app = express();
const router = express.Router();

app.use('/', router);

router.route('/').get((req, res) => {
   const data = fs.createReadStream('./text.txt');
   data.pipe(res);
});

http.createServer(app).listen(5000, () => {
    console.log('5000번 대기 중');
});
```
<br>
파일을 한번에 읽어온다면 파일 전체를 한 번에 메모리에 올리기 때문에 메모리 사용 측면에서 매우 비효율적이다. 하지만 스트림을 이용하여 파일을 읽는다면 스트림에 크기 만큼만 메모리에 올리면 되기 때문에 메모리 사용 측면에서 효율적이다. 또한 파일을 한 번에 읽어 온다면 파일 한계치를 사용자가 조정해줘야 되지만 스트림은 그럴 필요가 없다.

## 노드에서 사용하는 스트림의 종류
1. readable : 소비하는 데이터의 추상화 형태, fs.createReadStream 함수
2. writable : 데이터를 기록할수 있는 종착점을 추상화, fs.createWriteStream 함수
3. duplex : 읽기와 쓰기 동시 가능, TCP 소켓
4. Transform : 기본적인 duplex 형태, 데이터를 읽고 쓸 때 수정/변환이 가능한 스트림

## 참고자료
[genesis8](https://genesis8.tistory.com/230)
[github](https://github.com/FEDevelopers/tech.description/wiki/Node.js-Stream-%EB%8B%B9%EC%8B%A0%EC%9D%B4-%EC%95%8C%EC%95%84%EC%95%BC%ED%95%A0-%EB%AA%A8%EB%93%A0-%EA%B2%83)
