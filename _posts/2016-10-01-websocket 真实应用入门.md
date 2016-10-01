---
layout: post
title: "websocket 真实应用入门"
tags: ['nodejs', 'websocket', 'redis', 'socket.io', '频率限制']
---

websocket 入门在网上已经有很多教程了，今天我们用一下实例来学习一下 。

使用到的代码，我放到了 <https://github.com/tiseheaini/order-notice>



就像下面这样，当你有新的订单时会有一段语音提示你。

![websocket-bootstrap](/images/2016/10/websocket-bootstrap.png)

首先，我们需要新建一个项目，暂且叫它 `order-notice`  

项目会使用到

```bash
npm init .
npm install --save express socket.io ioredis
```

我们先来讲一下实现思路

1. 将信息发到 redis 广播中。
2. nodejs 将接受到的信息通过推送给前端。
3. 前端拿到信息后，播放声音。

![WX20190404-012006](/images/2016/10/websocket-step.png)

index.html 

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>订单通知页面</title>
  <style>
      .index .container {
        width: 1170px;
        overflow: hidden;
        margin: 0 auto;
        margin-top: 40px;
        position: absolute;
        top: 50%;
        transform: translate(-50%, -50%);
        left: 50%;
      }

      .index .container .notice-board {
        text-align: center;
        float: left;
        width: 230px;
        height: 285px;
        margin: 0px 25px;
        color: white;
      }

      .index .container .board-title {
        width: 100%;
        height: 132px;
        line-height: 132px;
        font-size: 35px;
      }

      .index .container .board-content {
        font-size: 95px;
      }

      .index .order-untreated { background-color: #22A12A; }

      .index .order-new { background-color: #DD0010; }

      .index .order-completed { background-color: #118DE3; }

      .index .teacher-untreated { background-color: #F5A641; }

      .index .resume-count    { background-color: #00C0EF; }
      .index .organ-count     { background-color: #DD4B39; }
      .index .organ-order     { background-color: #00A65A; }
      .index .website-article { background-color: #D88B10; }
      .index .college-article { background-color: #D2D6DE; }
  </style>
</head>
<body>
  <div class="index">
    <div class="container">
      <div class="notice-board order-untreated">
        <div class="board-title">未处理订单</div>
        <div class="board-content">10</div>
      </div>
      <div class="notice-board order-new">
        <div class="board-title">新添加订单</div>
        <div class="board-content">9</div>
      </div>
      <div class="notice-board order-completed">
        <div class="board-title">已完成套课</div>
        <div class="board-content">20</div>
      </div>
      <div class="notice-board teacher-untreated">
        <div class="board-title">未处理教师</div>
        <div class="board-content">110</div>
      </div>
    </div>
  </div>
</body>
</html>

```

这样，你就可以看到上面的页面的，有了这些只能有一个简单页面了，你需要做的是链接 websocket 服务器，当服务器上有通知时，做出相应的动作。



index.js

```js
var express = require('express');
var app = express();
var http = require('http').Server(app);
var io = require('socket.io')(http);
var Redis = require('ioredis');
var redis = new Redis();

app.use(express.static('public'));

redis.subscribe('orderChannel');

app.get('/', function(req, res){
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', function(socket){
  redis.on("message", function(channel, message) {
    if (channel == "orderChannel") {
      io.emit('order notice', "msg");
    }
  });
});

http.listen(8090, function(){
  console.log('listening on *:8090');
});
```



这时需要将 index.html 改一下了。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>微邻订单通知页面</title>
  <script src="https://cdn.socket.io/socket.io-1.2.0.js"></script>
  <script src="https://cdn.bootcss.com/jquery/1.11.1/jquery.min.js"></script>
  <style>
      .index .container {
        width: 1170px;
        overflow: hidden;
        margin: 0 auto;
        margin-top: 40px;
        position: absolute;
        top: 50%;
        transform: translate(-50%, -50%);
        left: 50%;
      }

      .index .container .notice-board {
        text-align: center;
        float: left;
        width: 230px;
        height: 285px;
        margin: 0px 25px;
        color: white;
      }

      .index .container .board-title {
        width: 100%;
        height: 132px;
        line-height: 132px;
        font-size: 35px;
      }

      .index .container .board-content {
        font-size: 95px;
      }

      .index .order-untreated { background-color: #22A12A; }

      .index .order-new { background-color: #DD0010; }

      .index .order-completed { background-color: #118DE3; }

      .index .teacher-untreated { background-color: #F5A641; }

      .index .resume-count    { background-color: #00C0EF; }
      .index .organ-count     { background-color: #DD4B39; }
      .index .organ-order     { background-color: #00A65A; }
      .index .website-article { background-color: #D88B10; }
      .index .college-article { background-color: #D2D6DE; }
  </style>
</head>
<body>
  <div class="index">
    <div class="container">
      <div class="notice-board order-untreated">
        <div class="board-title">未处理订单</div>
        <div class="board-content">10</div>
      </div>
      <div class="notice-board order-new">
        <div class="board-title">新添加订单</div>
        <div class="board-content">9</div>
      </div>
      <div class="notice-board order-completed">
        <div class="board-title">已完成套课</div>
        <div class="board-content">20</div>
      </div>
      <div class="notice-board teacher-untreated">
        <div class="board-title">未处理教师</div>
        <div class="board-content">110</div>
      </div>
    </div>

    <div class="resource-file">
      <audio id="notice-new">
          <source = src="text2audio-new.mp3" type="audio/mp3">
      </audio>
      <audio id="notice-completed">
          <source = src="text2audio-completed.mp3" type="audio/mp3">
      </audio>
      <audio id="notice-music">
          <source = src="text2audio-teacher.mp3" type="audio/mp3">
      </audio>
    </div>
  </div>

  <script>
    function updateOrderData(){
        console.log('updateOrderData();' + new Date());
        $.ajax({
          type: 'get',
          url: 'http://weilinky.linkerlab.net/api/notice-order-status',
          dataType:'jsonp',
          jsonp: 'callback',
          jsonpCallback: 'updateOrderCallback'
        });
    }
    function updateOrderCallback(data){
        $(".order-untreated .board-content").text(data['order-untreated']);
        $(".order-new .board-content").text(data['order-new']);
        $(".order-completed .board-content").text(data['order-completed']);
        $(".teacher-untreated .board-content").text(data['teacher-untreated']);
    }

    var socket = io();
    socket.on('orderChannel', function(message){
      updateOrderData();
      switch (message) {
        case "order-untreated":
          console.log("order-untreated");
          break;
        case "order-new":
          var audio = $("#notice-new");
          audio[0].currentTime = 0;
          audio[0].play();
          break;
        case "order-completed":
          var audio = $("#notice-completed");
          audio[0].currentTime = 0;
          audio[0].play();
          break;
        case "teacher-untreated":
          var audio = $("#notice-music");
          audio[0].currentTime = 0;
          audio[0].play();
          break;
        default:
          console.log("default");
      }
    });

    $(function(){
      updateOrderData();
    });
  </script>
</body>
</html>

```

