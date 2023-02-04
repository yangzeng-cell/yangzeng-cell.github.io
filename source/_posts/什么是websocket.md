---
title: 什么是websocket
date: 2022-09-24 01:03:18
tags:
- [websocket]
categories:
- [计算机网络,websocket]
---

##

webSocket是一种网络传输协议，可以在单个TCP连接上进行全双工通信，位于OSI模型的应用层，webSocket协议在2011年由IETF标准化为RFC6455,由RFC7936补充规范，WEB IDL中的webSocket api由W3C标准化，websocket是一种和http不同的协议，但是都是属于OSI模型的应用层，可以通过HTTP的80端口和443端口进行通信，支持HTTP代理和中介，和http协议兼容，为了实现兼容性，webSocket进行握手使用HTTP Upgrade头从http协议改成webSocket协议，websocket支持浏览器和服务器进行双向通信，可以进行实时通信，不需要客户端发起请求，目前大部分浏览器都支持websocket,和HTTP不同，websocket支持全双工通信，websocket还可以TCP之上实现消息流

websocket协议有两种标识符分别是ws和wss，分别是明文传输和加密传输。websocket不支持#，除了这些，其他都和http相同

```
ws://example.com/wsapi
wss://secure.example.com/wsapi
```

#### websocket的优点

- 比较少的控制开销，在服务器和客户端通信的时候，用于协议控制的数据包头部相对较小，在不包含扩展的情况下，对于服务器到客户端的内容，此头部大小只有2到10字节，对于客户端到服务器的内容，此头部还需要加额外的4字节的掩码，相对于http每次都要携带完整的头部，明显减少了开销

- 更强的实时性，由于协议是全双工的，所以服务器可以随时主动的给客户端发送数据，相对于HTTP请求需要客户端发送请求并且等待服务器响应，减少了等待时间，即使和Comet等类似的长轮询比较，它也可以在短时间传递更多的数据

- 保持连接状态，与HTTP不同的是，webSocket需要先建立连接，这就使得其成为一种有状态的协议，之后通信时可以省略部分状态信息，而http每次请求可能需要携带验证信息

- 更好的二进制支持，webSocket定义了二进制帧，他会比HTTP更i轻松的处理二进制文件

- 更好的压缩效果，相对于HTTP压缩，websocket在适当的扩展的支持下，可以沿用之前的内容的上下文，在传递类似的数据的时候可以显著的提高压缩率

  

webSocket是独立的，建立在TCP上的协议，websocket通过HTTP/1.1的101状态吗继续握手，为了建立Websocket连接，需要通过浏览器发出请求，之后服务器进行回应，这个过程通常称为“[握手](https://zh.m.wikipedia.org/wiki/握手_(技术))”（Handshaking）

#### 例子

客户端请求：

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

服务器的回应

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

- Connection必须设置Upgrade，表示客户端希望连接升级。
- Upgrade字段必须设置Websocket，表示希望升级到Websocket协议。
- Sec-WebSocket-Key是随机的字符串，服务器端会用这些数据来构造出一个SHA-1的信息摘要。把“Sec-WebSocket-Key”加上一个特殊字符串“258EAFA5-E914-47DA-95CA-C5AB0DC85B11”，然后计算[SHA-1](https://zh.m.wikipedia.org/wiki/SHA-1)摘要，之后进行[Base64](https://zh.m.wikipedia.org/wiki/Base64)编码，将结果做为“Sec-WebSocket-Accept”头的值，返回给客户端。如此操作，可以尽量避免普通HTTP请求被误认为Websocket协议。
- Sec-WebSocket-Version 表示支持的Websocket版本。RFC6455要求使用的版本是13，之前草案的版本均应当弃用。
- Origin字段是必须的。如果缺少origin字段，WebSocket服务器需要回复HTTP 403 状态码（禁止访问）。[[16\]](https://zh.m.wikipedia.org/zh-hans/WebSocket#cite_note-16)
- 其他一些定义在HTTP协议中的字段，如[Cookie](https://zh.m.wikipedia.org/wiki/Cookie)等，也可以在Websocket中使用。



#### websocket的基本使用

##### 客户端

```javascript
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};      
```

> ```javascript
> var ws = new WebSocket('ws://localhost:8080');
> ```

wenSocket作为构造函数，用于新建WebSocket实例，执行完上面的例子之后，客户端就会和服务器进行连接

##### webSocket.readyState

readyState属性是返回当前状态，共有四种状态

- CONNECTING：值为0，表示正在连接。
- OPEN：值为1，表示连接成功，可以通信了。
- CLOSING：值为2，表示连接正在关闭。
- CLOSED：值为3，表示连接已经关闭，或者打开连接失败。

```javascript
switch (ws.readyState) {
  case WebSocket.CONNECTING:
    // do something
    break;
  case WebSocket.OPEN:
    // do something
    break;
  case WebSocket.CLOSING:
    // do something
    break;
  case WebSocket.CLOSED:
    // do something
    break;
  default:
    // this never happens
    break;
}
```

##### open属性监听连接之后的回调函数

```javascript
ws.onopen = function () {
  ws.send('Hello Server!');
}
//执行多个回调函数可以使用addEventListener
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
```

##### 实例对象的`onclose`属性，用于指定连接关闭后的回调函数

```js
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};

ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```

实例对象的`onmessage`属性，用于指定收到服务器数据后的回调函数

```javascript
ws.onmessage = function(event) {
  var data = event.data;
  // 处理数据
};

ws.addEventListener("message", function(event) {
  var data = event.data;
  // 处理数据
});
```

注意，服务器数据可能是文本，也可能是二进制数据（`blob`对象或`Arraybuffer`对象

```javascript
ws.onmessage = function(event){
  if(typeof event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```

除了动态判断收到的数据类型，也可以使用`binaryType`属性，显式指定收到的二进制数据类型。

> ```javascript
> // 收到的是 blob 数据
> ws.binaryType = "blob";
> ws.onmessage = function(e) {
>   console.log(e.data.size);
> };
> 
> // 收到的是 ArrayBuffer 数据
> ws.binaryType = "arraybuffer";
> ws.onmessage = function(e) {
>   console.log(e.data.byteLength);
> };
> ```

实例对象的`send()`方法用于向服务器发送数据。

> ```javascript
> ws.send('your message');
> ```

发送blob对象的例子

```javascript
var file = document
  .querySelector('input[type="file"]')
  .files[0];
ws.send(file);
```

发送ArrayBuffer对象的例子

```javascript
// Sending canvas ImageData as ArrayBuffer
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```

实例对象的`bufferedAmount`属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束

```javascript
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

实例对象的`onerror`属性，用于指定报错时的回调函数。

> ```javascript
> socket.onerror = function(event) {
>   // handle error event
> };
> 
> socket.addEventListener("error", function(event) {
>   // handle error event
> })
> ```

#### 服务端的实现

如果用node实现websocket

- [µWebSockets](https://github.com/uWebSockets/uWebSockets)
- [Socket.IO](http://socket.io/)
- [WebSocket-Node](https://github.com/theturtle32/WebSocket-Node)
