---
title: secket.io笔记
date: 2022-09-24 15:31:44
tags:
- [socket.io]
catagories:
- [计算机网络,websocket]
---

目前是4.x版本

## 什么是 Socket.IO

Socket.IO 是一个库，可以在客户端和服务器之间实现 **低延迟**, **双向** 和 **基于事件的** 通信。

![Diagram of a communication between a server and a client](https://socket.io/zh-CN/images/bidirectional-communication2.png)

它建立在 [WebSocket](https://fr.wikipedia.org/wiki/WebSocket) 协议之上，并提供额外的保证，例如回退到 HTTP 长轮询或自动重新连接。

这里只介绍socket.io在web端的使用

这是一个websocket的实例

*服务器* (基于 [ws](https://github.com/websockets/ws))

```
npm i ws
```



```js
import { WebSocketServer } from "ws";

const server = new WebSocketServer({ port: 3000 });

server.on("connection", (socket) => {
  // 向客户端发送消息
  socket.send(JSON.stringify({
    type: "hello from server",
    content: [ 1, "2" ]
  }));

  // 从客户端接收消息
  socket.on("message", (data) => {
    const packet = JSON.parse(data);

    switch (packet.type) {
      case "hello from server":
        // ...
        break;
    }
  });
});
```

客户端

```js
import { WebSocket } from "ws";

const socket = new WebSocket("ws://localhost:3000");

socket.addEventListener("open", () => {
  // 向服务器发送消息
  socket.send(JSON.stringify({
    type: "hello from server",
    content: [ 3, "4" ]
  }));
});

// 从服务器接收消息
socket.addEventListener("message", ({ data }) => {
  const packet = JSON.parse(data);

  switch (packet.type) {
    case "hello from server":
      // ...
      break;
  }
});
```

socket.io的示例,需要先安装socket.io和socket.io-client

服务端

```js
import { Server } from "socket.io";

const io = new Server(3000);

io.on("connection", (socket) => {
  // 向客户端发送消息
  socket.emit("hello from server", 1, "2", { 3: Buffer.from([4]) });

  // 从客户端接收消息
  socket.on("hello from server", (...args) => {
    // ...
  });
});
```

*客户端*

```js
import { io } from "socket.io-client";

const socket = io("ws://localhost:3000");

// 向服务器发送消息
socket.emit("hello from server", 5, "6", { 7: Uint8Array.from([8]) });

// 从服务器接收消息
socket.on("hello from server", (...args) => {
  // ...
});
```

## Socket.IO 不是什么

Socket.IO **不是** WebSocket实现。是基于WebSocket协议

尽管 Socket.IO 确实在可能的情况下使用 WebSocket 进行传输，但它为每个数据包添加了额外的元数据。这就是为什么 WebSocket 客户端将无法成功连接到 Socket.IO 服务器，而 Socket.IO 客户端也将无法连接到普通 WebSocket 服务器。

```
// 警告：客户端将无法连接！
const socket = io("ws://echo.websocket.org");
```

## Socket.IO特点

### HTTP 长轮询回退

如果无法建立 WebSocket 连接，连接将回退到 HTTP 长轮询。

### 自动重新连接

在某些特定情况下，服务器和客户端之间的 WebSocket 连接可能会中断，而双方都不知道链接的断开状态。

这就是为什么 Socket.IO 包含一个心跳机制，它会定期检查连接的状态。

当客户端最终断开连接时，它会以指数回退延迟自动重新连接，以免使服务器不堪重负。

### 数据包缓冲

当客户端断开连接时，数据包会自动缓冲，并在重新连接时发送

### 收到后的回调

Socket.IO 提供了一种方便的方式来发送事件和接收响应	

*发件人*

```js
socket.emit("hello", "world", (response) => {
  console.log(response); // "got it"
});
```

*接收者*

```js
socket.on("hello", (arg, callback) => {
  console.log(arg); // "world"
  callback("got it!");
});
```

### 广播

[在服务器端，您可以向所有连接的客户端](https://socket.io/zh-CN/docs/v4/broadcasting-events/)或[客户端的子集](https://socket.io/zh-CN/docs/v4/rooms/)发送事件：

```js
// 到所有连接的客户端
io.emit("hello");

// 致“news”房间中的所有连接客户端
io.to("news").emit("hello");
```

### 多路复用

命名空间允许您在单个共享连接上拆分应用程序的逻辑。例如，如果您想创建一个只有授权用户才能加入的“管理员”频道，这可能很有用。

```js
io.on("connection", (socket) => {
  // 普通用户
});

io.of("/admin").on("connection", (socket) => {
  // 管理员用户
});
```

## 运作原理

Socket.IO 服务器 (Node.js) 和 Socket.IO 客户端（浏览器, Node.js, or [其他编程语言](https://socket.io/zh-CN/docs/v4/#what-socketio-is)）之间的双向通道尽可能使用[WebSocket 连接](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)建立，并将使用 HTTP 长轮询作为后备。

Socket.IO 代码库分为两个不同的层：

- 底层通道：我们称之为Engine.IO，Socket.IO内部的引擎
- 高级 API：Socket.IO 本身

## Engine.IO

Engine.IO 负责建立服务器和客户端之间的低级连接。它处理：

- 各种[传输](https://socket.io/zh-CN/docs/v4/how-it-works/#transports)和[升级机制](https://socket.io/zh-CN/docs/v4/how-it-works/#upgrade-mechanism)
- [断线检测](https://socket.io/zh-CN/docs/v4/how-it-works/#disconnection-detection)

源代码可以在这里找到：

- 服务器：https://github.com/socketio/engine.io
- 客户端：https://github.com/socketio/engine.io-client
- 解析器：https://github.com/socketio/engine.io-parser
- 协议说明：https://github.com/socketio/engine.io-protocol

### 传输

目前有两种实现的传输:

- [HTTP 长轮询](https://socket.io/zh-CN/docs/v4/how-it-works/#http-long-polling)
- [WebSocket](https://socket.io/zh-CN/docs/v4/how-it-works/#websocket)

#### HTTP 长轮询

THTTP 长轮询传输（也简称为“轮询”）由连续的 HTTP 请求组成：

- 长时间运行的 `GET` 请求，用于从服务器接收数据
- 短时 `POST` 请求，用于向服务器发送数据

由于传输的性质，可能会在同一个 HTTP 请求中连接并发送连续的请求。

#### WebSocket

WebSocket 传输由[WebSocket 连接](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)组成，它在服务器和客户端之间提供双向和低延迟的通信通道。

由于传输的性质，每个发射都在其自己的 WebSocket 帧中发送（有些发射甚至可能导致两个不同的 WebSocket 帧，更多信息[在这里](https://socket.io/zh-CN/docs/v4/custom-parser/#the-default-parser)).

### 握手

在 Engine.IO 连接开始时，服务器发送一些信息：

```json
{
  "sid": "FSDjX-WRwSA4zTZMALqx",
  "upgrades": ["websocket"],
  "pingInterval": 25000,
  "pingTimeout": 20000
}
```

复制

- `sid` 是会话的ID，它必须包含在sid所有后续HTTP请求的查询参数中
- `upgrades` 数组包含服务器支持的所有“更好”传输的列表
- `pingInterval` 和 `pingTimeout` 值用于心跳

### 升级机制

默认情况下，客户端使用 HTTP 长轮询传输建立连接。

**为什么呢?**

虽然 WebSocket 显然是建立双向通信的最佳方式，但经验表明，由于企业代理、个人防火墙、防病毒软件...

从用户的角度来看，一个不成功的 WebSocket 连接可以转化为等待实时应用程序开始交换数据的至少 10 秒。 这在**感知**上会损害用户体验。

总而言之，Engine.io首先关注可靠性和用户体验，其次关注潜在的 UX 改进和提高服务器性能。

要升级，客户端将：

- 确保其传出缓冲区为空
- 将当前传输设置为只读模式
- 尝试与其他传输建立连接
- 如果成功，关闭第一个传输

您可以在浏览器的网络监视器中查看：

![Successful upgrade](https://socket.io/zh-CN/assets/images/network-monitor-2e47dbe233100aa290595f8687a9fcba.png)

1. 握手 (包含会话 ID — 此处, `zBjrh...AAAK` — 用于后续请求)
2. 发送数据 (HTTP 长轮询)
3. 接收数据 (HTTP 长轮询)
4. 升级 (WebSocket)
5. 接收数据 (HTTP 长轮询, WebSocket连接建立成功后关闭)

### 断线检测

Engine.IO 连接在以下情况下被视为关闭：

- 一个 HTTP 请求（GET 或 POST）失败（例如，当服务器关闭时）
- WebSocket 连接关闭（例如，当用户关闭其浏览器中的选项卡时）
- `socket.disconnect()` 在服务器端或客户端调用

还有一个心跳机制检查服务器和客户端之间的连接是否仍然正常运行：

在给定的时间间隔（ `pingInterval`握手中发送的值），服务器发送一个 PING 数据包，客户端有几秒钟（该`pingTimeout`值）发送一个 PONG 数据包。如果服务器没有收到返回的 PONG 数据包，则认为连接已关闭。反之，如果客户端在 内没有收到 PING 包`pingInterval + pingTimeout`，则认为连接已关闭。

## Socket.IO

Socket.IO 通过 Engine.IO 连接提供了一些附加功能：

- 自动重连
- [数据包缓冲](https://socket.io/zh-CN/docs/v4/client-offline-behavior/#buffered-events)
- [收到后的回调](https://socket.io/zh-CN/docs/v4/emitting-events/#acknowledgements)
- 广播 [到所有客户端](https://socket.io/zh-CN/docs/v4/broadcasting-events/) 或 [客户端的子集](https://socket.io/zh-CN/docs/v4/rooms/)（我们称之为“房间”）
- [多路复用](https://socket.io/zh-CN/docs/v4/namespaces/)（我们称之为“命名空间”）

源代码可以在这里找到：

- 服务器：https://github.com/socketio/socket.io
- 客户端：https://github.com/socketio/socket.io-client
- 解析器：https://github.com/socketio/socket.io-parser
- 协议说明：https://github.com/socketio/socket.io-protocol

# 服务器安装

```sh
npm install socket.io
```

安装特定版本：

```sh
npm install socket.io@version
```

默认情况下，Socket.IO 使用[ws](https://www.npmjs.com/package/ws)包提供的 WebSocket 服务器。

# 服务器初始化

## 初始化

### 只使用Socket

```js
//commonjs
const { Server } = require("socket.io");

const io = new Server({ /* options */ });

io.on("connection", (socket) => {
  // ...
});

io.listen(3000);
```

```js
//esmodule
import { Server } from "socket.io";

const io = new Server({ /* options */ });

io.on("connection", (socket) => {
  // ...
});

io.listen(3000);
```

```js
import { Server } from "socket.io";

const io = new Server({ /* options */ });

io.on("connection", (socket) => {
  // ...
});

io.listen(3000);
```

您还可以将端口作为第一个参数传递：

```js
import { Server } from "socket.io";

const io = new Server(3000, { /* options */ });

io.on("connection", (socket) => {
  // ...
});
```

这隐式启动了一个 Node.js[HTTP 服务器](https://nodejs.org/docs/latest/api/http.html#http_class_http_server)，可以通过 `io.httpServer`。

### 使用HTTP服务器

```js
import { createServer } from "http";
import { Server } from "socket.io";

const httpServer = createServer();
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

### 使用HTTPS服务器

```js
import { readFileSync } from "fs";
import { createServer } from "https";
import { Server } from "socket.io";

const httpServer = createServer({
  key: readFileSync("/path/to/my/key.pem"),
  cert: readFileSync("/path/to/my/cert.pem")
});

const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

使用客户端证书身份验证：

*服务器*

```js
import { readFileSync } from "fs";
import { createServer } from "https";
import { Server } from "socket.io";

const httpServer = createServer({
  key: readFileSync("/path/to/server-key.pem"),
  cert: readFileSync("/path/to/server-cert.pem"),
  requestCert: true,
  ca: [
    readFileSync("/path/to/client-cert.pem")
  ]
});

const io = new Server(httpServer, { /* options */ });

io.engine.on("connection", (rawSocket) => {
  // if you need the certificate details (it is no longer available once the handshake is completed)
  rawSocket.peerCertificate = rawSocket.request.client.getPeerCertificate();
});

io.on("connection", (socket) => {
  console.log(socket.conn.peerCertificate);
  // ...
});

httpServer.listen(3000);
```

*客户端*

```js
import { readFileSync } from "fs";
import { io } from "socket.io-client";

const socket = io("https://example.com", {
  key: readFileSync("/path/to/client-key.pem"),
  cert: readFileSync("/path/to/client-cert.pem"),
  ca: [
    readFileSync("/path/to/server-cert.pem")
  ]
});
```

### 使用HTTP/2服务器

```js
import { readFileSync } from "fs";
import { createSecureServer } from "http2";
import { Server } from "socket.io";

const httpServer = createSecureServer({
  allowHTTP1: true,
  key: readFileSync("/path/to/my/key.pem"),
  cert: readFileSync("/path/to/my/cert.pem")
});

const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

### 使用 Express

```js
//esmodule
import express from "express";
import { createServer } from "http";
import { Server } from "socket.io";

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

```js
//ts
import * as express from "express";
import { createServer } from "http";
import { Server } from "socket.io";

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

<u>在这里使用`app.listen(3000)`将不起作用，因为它会创建一个新的 HTTP 服务器。</u>

### 使用 Koa

```js
import Koa from "koa";
import { createServer } from "http";
import { Server } from "socket.io";

const app = new Koa();
const httpServer = createServer(app.callback());
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

```js
import * as Koa from "koa";
import { createServer } from "http";
import { Server } from "socket.io";

const app = new Koa();
const httpServer = createServer(app.callback());
const io = new Server(httpServer, { /* options */ });

io.on("connection", (socket) => {
  // ...
});

httpServer.listen(3000);
```

### 使用 µWebSockets.js 

```js
import { App } from "uWebSockets.js";
import { Server } from "socket.io";

const app = new App();
const io = new Server();

io.attachApp(app);

io.on("connection", (socket) => {
  // ...
});

app.listen(3000, (token) => {
  if (!token) {
    console.warn("port already in use");
  }
});
```

### 使用 Fastify

您需要注册[`fastify-socket.io`](https://github.com/alemagio/fastify-socket.io)插件：

- CommonJS

  ```js
  const fastify = require("fastify");
  const fastifyIO = require("fastify-socket.io");
  
  const server = fastify();
  server.register(fastifyIO);
  
  server.get("/", (req, reply) => {
    server.io.emit("hello");
  });
  
  server.ready().then(() => {
    // we need to wait for the server to be ready, else `server.io` is undefined
    server.io.on("connection", (socket) => {
      // ...
    });
  });
  
  server.listen(3000);
  ```

  

- ES modules

- ```js
  import fastify from "fastify";
  import fastifyIO from "fastify-socket.io";
  
  const server = fastify();
  server.register(fastifyIO);
  
  server.get("/", (req, reply) => {
    server.io.emit("hello");
  });
  
  server.ready().then(() => {
    // we need to wait for the server to be ready, else `server.io` is undefined
    server.io.on("connection", (socket) => {
      // ...
    });
  });
  
  server.listen(3000);
  ```

  

- TypeScript

```ts
import fastify from "fastify";
import fastifyIO from "fastify-socket.io";

const server = fastify();
server.register(fastifyIO);

server.get("/", (req, reply) => {
  server.io.emit("hello");
});

server.ready().then(() => {
  // we need to wait for the server to be ready, else `server.io` is undefined
  server.io.on("connection", (socket) => {
    // ...
  });
});

server.listen(3000);
```

# 服务器实例

服务器实例（通常`io`在代码示例中调用）具有一些可能在您的应用程序中使用的属性。

它还继承了[主命名空间](https://socket.io/zh-CN/docs/v4/namespaces/#main-namespace)的所有方法，例如[`namespace.use()`](https://socket.io/zh-CN/docs/v4/server-api/#namespaceusefn)（参见[此处](https://socket.io/zh-CN/docs/v4/middlewares/)）或 [`namespace.allSockets()`](https://socket.io/zh-CN/docs/v4/server-api/#namespaceallsockets).

## 服务器#engine

对底层 Engine.IO 服务器的引用。

它可用于获取当前连接的客户端数量：

```js
const count = io.engine.clientsCount;
// 根据您的用法，可能与主命名空间中的Socket实例计数相似或不相似
const count2 = io.of("/").sockets.size;
```

或者生成自定义会话 ID（`sid`查询参数）：

```js
const uuid = require("uuid");

io.engine.generateId = (req) => {
  return uuid.v4(); // 必须在所有socket.io服务器上都是唯一的
}
```

从`socket.io@4.1.0`开始，Engine.IO 服务器发出三个特殊事件：

- `initial_headers`: 将在编写会话的第一个 HTTP 请求（握手）的响应标头之前发出，允许您自定义它们。

```js
io.engine.on("initial_headers", (headers, req) => {
  headers["test"] = "123";
  headers["set-cookie"] = "mycookie=456";
});
```

- `headers`: 将在编写会话的每个 HTTP 请求的响应头之前发出（包括 WebSocket 升级），允许您自定义它们。

```js
io.engine.on("headers", (headers, req) => {
  headers["test"] = "789";
});
```

- `connection_error`: 当连接异常关闭时发出

```js
io.engine.on("connection_error", (err) => {
  console.log(err.req);      // the request object
  console.log(err.code);     // the error code, for example 1
  console.log(err.message);  // the error message, for example "Session ID unknown"
  console.log(err.context);  // some additional error context
});
```

以下是可能的错误代码列表：

| Code | Message                        |
| ---- | ------------------------------ |
| 0    | "Transport unknown"            |
| 1    | "Session ID unknown"           |
| 2    | "Bad handshake method"         |
| 3    | "Bad request"                  |
| 4    | "Forbidden"                    |
| 5    | "Unsupported protocol version" |

### `socketsJoin`

此方法使匹配的 Socket 实例加入指定的房间：

```js
// make all Socket instances join the "room1" room
io.socketsJoin("room1");

// make all Socket instances in the "room1" room join the "room2" and "room3" rooms
io.in("room1").socketsJoin(["room2", "room3"]);

// make all Socket instances in the "room1" room of the "admin" namespace join the "room2" room
io.of("/admin").in("room1").socketsJoin("room2");

// this also works with a single socket ID
io.in(theSocketId).socketsJoin("room1");
```

### `socketsLeave`

该方法使匹配的 Socket 实例离开指定房间：

```js
// make all Socket instances leave the "room1" room
io.socketsLeave("room1");

// make all Socket instances in the "room1" room leave the "room2" and "room3" rooms
io.in("room1").socketsLeave(["room2", "room3"]);

// make all Socket instances in the "room1" room of the "admin" namespace leave the "room2" room
io.of("/admin").in("room1").socketsLeave("room2");

// this also works with a single socket ID
io.in(theSocketId).socketsLeave("room1");
```

### `disconnectSockets`

此方法使匹配的 Socket 实例断开连接：

```js
// make all Socket instances disconnect
io.disconnectSockets();

// make all Socket instances in the "room1" room disconnect (and discard the low-level connection)
io.in("room1").disconnectSockets(true);

// make all Socket instances in the "room1" room of the "admin" namespace disconnect
io.of("/admin").in("room1").disconnectSockets();

// this also works with a single socket ID
io.of("/admin").in(theSocketId).disconnectSockets();
```

### `fetchSockets`

此方法返回匹配的 Socket 实例：

```js
// return all Socket instances of the main namespace
const sockets = await io.fetchSockets();

// return all Socket instances in the "room1" room of the main namespace
const sockets = await io.in("room1").fetchSockets();

// return all Socket instances in the "room1" room of the "admin" namespace
const sockets = await io.of("/admin").in("room1").fetchSockets();

// this also works with a single socket ID
const sockets = await io.in(theSocketId).fetchSockets();
```

上例中的`sockets`变量是一个对象数组，暴露了通常的 Socket 类的一个子集：

```js
for (const socket of sockets) {
  console.log(socket.id);
  console.log(socket.handshake);
  console.log(socket.rooms);
  console.log(socket.data);
  socket.emit(/* ... */);
  socket.join(/* ... */);
  socket.leave(/* ... */);
  socket.disconnect(/* ... */);
}
```

`data`属性是一个任意对象，可用于在 Socket.IO 服务器之间共享信息：

```js
// server A
io.on("connection", (socket) => {
  socket.data.username = "alice";
});

// server B
const sockets = await io.fetchSockets();
console.log(sockets[0].data.username); // "alice"
```

### `serverSideEmit`

此方法允许在[多服务器设置](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/)中向集群的其他 Socket.IO 服务器发出事件。

语法：

```js
io.serverSideEmit("hello", "world");
```

在接收方：

```js
io.on("hello", (arg1) => {
  console.log(arg1); // prints "world"
});
```

也支持确认：

```js
// server A
io.serverSideEmit("ping", (err, responses) => {
  console.log(responses[0]); // prints "pong"
});

// server B
io.on("ping", (cb) => {
  cb("pong");
});
```

笔记：

- `connection`，`connect` 和 `new_namespace` 字符串是保留的，不能在您的应用程序中使用。
- 您可以发送任意数量的参数，但目前不支持二进制结构（参数数组将被`JSON.stringify`-ed）

例子：

```js
io.serverSideEmit("hello", "world", 1, "2", { 3: "4" });
```

- 如果其他 Socket.IO 服务器在给定延迟后没有响应，则调用确认回调可能会出错

```js
io.serverSideEmit("ping", (err, responses) => {
  if (err) {
    // at least one Socket.IO server has not responded
    // the 'responses' array contains all the responses already received though
  } else {
    // success! the 'responses' array contains one object per other Socket.IO server in the cluster
  }
});
```

## Events

Server 实例发出一个事件（好吧，从技术上讲是两个，但`connect`它是`connection`的别名）：

- [`connection`](https://socket.io/zh-CN/docs/v4/server-instance/#connection)

### `connection`

此事件在新连接时触发。第一个参数是一个[Socket实例](https://socket.io/zh-CN/docs/v4/server-socket-instance/).

```js
io.on("connection", (socket) => {
  // ...
});
```

# Socket 实例（服务器端）

`Socket`是与客户端交互的基础类。它继承了 Node.js[EventEmitter](https://nodejs.org/api/events.html#class-eventemitter)的所有方法，例如[emit](https://socket.io/zh-CN/docs/v4/server-api/#socketemiteventname-args), [on](https://socket.io/zh-CN/docs/v4/server-api/#socketoneventname-callback), [once](https://socket.io/zh-CN/docs/v4/server-api/#socketonceeventname-listener) 或 [removeListener](https://socket.io/zh-CN/docs/v4/server-api/#socketremovelistenereventname-listener).

![Bidirectional communication between server and client](https://socket.io/zh-CN/images/bidirectional-communication-socket.png)



除了：

- [发出](https://socket.io/zh-CN/docs/v4/emitting-events/#basic-emit) 和 [监听](https://socket.io/zh-CN/docs/v4/listening-to-events/) 事件
- [广播事件](https://socket.io/zh-CN/docs/v4/broadcasting-events/#to-all-connected-clients-except-the-sender)
- [加入和离开房间](https://socket.io/zh-CN/docs/v4/rooms/#joining-and-leaving)

Socket 实例有一些可能在您的应用程序中使用的属性：

## Socket#id

每个新连接都分配有一个随机的 20 个字符的标识符。

此标识符与客户端的值同步。

```js
// server-side
io.on("connection", (socket) => {
  console.log(socket.id); // ojIckSD2jqNzOqIrAGzL
});

// client-side
socket.on("connect", () => {
  console.log(socket.id); // ojIckSD2jqNzOqIrAGzL
});
```

创建后，Socket 会加入由其自己的 id 标识的房间，这意味着您可以将其用于私人消息传递：

```js
io.on("connection", socket => {
  socket.on("private message", (anotherSocketId, msg) => {
    socket.to(anotherSocketId).emit("private message", socket.id, msg);
  });
});
```

注意：您不能覆盖此标识符，因为它在 Socket.IO 代码库的多个部分中使用。

## Socket#handshake

此对象包含有关在 Socket.IO 会话开始时发生的握手的一些详细信息。

```text
{
  headers: /* the headers of the initial request */
  query: /* the query params of the initial request */
  auth: /* the authentication payload */
  time: /* the date of creation (as string) */
  issued: /* the date of creation (unix timestamp) */
  url: /* the request URL string */
  address: /* the ip of the client */
  xdomain: /* whether the connection is cross-domain */
  secure: /* whether the connection is secure */
}
```

例子：

```json
{
  "headers": {
    "user-agent": "xxxx",
    "accept": "*/*",
    "host": "example.com",
    "connection": "close"
  },
  "query": {
    "EIO": "4",
    "transport": "polling",
    "t": "NNjNltH"
  },
  "auth": {
    "token": "123"
  },
  "time": "Sun Nov 22 2020 01:33:46 GMT+0100 (Central European Standard Time)",
  "issued": 1606005226969,
  "url": "/socket.io/?EIO=4&transport=polling&t=NNjNltH",
  "address": "::ffff:1.2.3.4",
  "xdomain": false,
  "secure": true
}
```

## Socket#rooms

这是对 Socket 当前所在[房间](https://socket.io/zh-CN/docs/v4/rooms/)的引用。

```js
io.on("connection", (socket) => {
  console.log(socket.rooms); // Set { <socket.id> }
  socket.join("room1");
  console.log(socket.rooms); // Set { <socket.id>, "room1" }
});
```

## Socket#data

可以与`fetchSockets()`实用程序方法结合使用的任意对象：

```js
// server A
io.on("connection", (socket) => {
  socket.data.username = "alice";
});

// server B
const sockets = await io.fetchSockets();
console.log(sockets[0].data.username); // "alice"
```

## Socket#conn

对底层 Engine.IO 套接字的引用（参见[此处](https://socket.io/zh-CN/docs/v4/how-it-works/)）。

```js
io.on("connection", (socket) => {
  console.log("initial transport", socket.conn.transport.name); // prints "polling"

  socket.conn.once("upgrade", () => {
    // called when the transport is upgraded (i.e. from HTTP long-polling to WebSocket)
    console.log("upgraded transport", socket.conn.transport.name); // prints "websocket"
  });

  socket.conn.on("packet", ({ type, data }) => {
    // called for each packet received
  });

  socket.conn.on("packetCreate", ({ type, data }) => {
    // called for each packet sent
  });

  socket.conn.on("drain", () => {
    // called when the write buffer is drained
  });

  socket.conn.on("close", (reason) => {
    // called when the underlying connection is closed
  });
});
```

## Additional attributes

只要您不覆盖任何现有属性，您就可以将任何属性附加到 Socket 实例并在以后使用它：

```js
// in a middleware
io.use(async (socket, next) => {
  try {
    const user = await fetchUser(socket);
    socket.user = user;
  } catch (e) {
    next(new Error("unknown user"));
  }
});

io.on("connection", (socket) => {
  console.log(socket.user);

  // in a listener
  socket.on("set username", (username) => {
    socket.username = username;
  });
});

```

## Socket middlewares

这些中间件看起来很像通常的[中间价](https://socket.io/zh-CN/docs/v4/middlewares/)，除了它们是为每个传入的数据包调用的：

```js
socket.use(([event, ...args], next) => {
  // do something with the packet (logging, authorization, rate limiting...)
  // do not forget to call next() at the end
  next();
});
```

复制

`next`也可以使用错误对象调用该方法。在这种情况下，事件将不会到达注册的事件处理程序，而`error`是会发出一个事件：

```js
io.on("connection", (socket) => {
  socket.use(([event, ...args], next) => {
    if (isUnauthorized(event)) {
      return next(new Error("unauthorized event"));
    }
    next();
  });

  socket.on("error", (err) => {
    if (err && err.message === "unauthorized event") {
      socket.disconnect();
    }
  });
});
```

复制

注意：此功能仅存在于服务器端。对于客户端，您可能对[catch-all listeners](https://socket.io/zh-CN/docs/v4/listening-to-events/#catch-all-listeners)感兴趣。

## Events[](https://socket.io/zh-CN/docs/v4/server-socket-instance/#events)

在服务器端，Socket 实例发出两个特殊事件：

- [`disconnect`](https://socket.io/zh-CN/docs/v4/server-socket-instance/#disconnect)
- [`disconnecting`](https://socket.io/zh-CN/docs/v4/server-socket-instance/#disconnecting)

### `disconnect`[](https://socket.io/zh-CN/docs/v4/server-socket-instance/#disconnect)

此事件由 Socket 实例在断开连接时触发。

```js
io.on("connection", (socket) => {
  socket.on("disconnect", (reason) => {
    // ...
  });
});
```

复制

以下是可能的原因列表：

| Reason                        | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| `server namespace disconnect` | socket被[socket.disconnect](https://socket.io/zh-CN/docs/v4/server-api/#socketdisconnectclose)强行断开 |
| `client namespace disconnect` | 客户端使用[socket.disconnect()](https://socket.io/zh-CN/docs/v4/client-api/#socketdisconnect)手动断开socket |
| `server shutting down`        | 服务器正在关闭                                               |
| `ping timeout`                | `pingTimeout` 客户端在延迟中没有发送 PONG 数据包             |
| `transport close`             | 连接已关闭（例如：用户失去连接，或网络从 WiFi 更改为 4G）    |
| `transport error`             | 连接遇到错误                                                 |

### `disconnecting`[](https://socket.io/zh-CN/docs/v4/server-socket-instance/#disconnecting)

当[Socket#rooms](https://socket.io/zh-CN/docs/v4/server-socket-instance/#socketrooms)集不为空时，此事件类似于`disconnect`但更早触发。

```js
io.on("connection", (socket) => {
  socket.on("disconnecting", (reason) => {
    for (const room of socket.rooms) {
      if (room !== socket.id) {
        socket.to(room).emit("user has left", socket.id);
      }
    }
  });
});
```

注意：这些事件以及`connect`, `connect_error`, `newListener` 和 `removeListener`是不应在您的应用程序中使用的特殊事件:

```js
// BAD, will throw an error
socket.emit("disconnect");
```

# 中间件

中间件函数是为每个传入连接执行的函数。

中间件函数可用于：

- logging
- authentication / authorization
- rate limiting

Note: this function will be executed only once per connection (even if the connection consists in multiple HTTP requests).

## 注册中间件

中间件函数可以访问[Socket 实例](https://socket.io/zh-CN/docs/v4/server-socket-instance/)和下一个注册的中间件函数。

```js
io.use((socket, next) => {
  if (isValid(socket.request)) {
    next();
  } else {
    next(new Error("invalid"));
  }
});
```

您可以注册几个中间件函数，它们将按顺序执行：

```
io.use((socket, next) => {
  next();
});

io.use((socket, next) => {
  next(new Error("thou shall not pass"));//error可以作为参数传递到下一层，其他不可以	
});

io.use((socket, next) => {
  // not executed, since the previous middleware has returned an error
  next();
});
```

请确保在任何情况下都调用`next（）`。 否则，连接将一直挂起，直到在给定超时后关闭。

**重要提示**：执行中间件时，Socket 实例实际上并未连接，这意味着`disconnect`如果连接最终失败，则不会发出任何事件。

例如，如果客户端手动关闭连接：

```js
// server-side
io.use((socket, next) => {
  setTimeout(() => {
    // next is called after the client disconnection
    next();
  }, 1000);

  socket.on("disconnect", () => {
    // not triggered
  });
});

io.on("connection", (socket) => {
  // not triggered
});

// client-side
const socket = io();
setTimeout(() => {
  socket.disconnect();
}, 500);
```

## 发送凭据

`auth`客户端可以使用以下选项发送凭据：

```js
// plain object
const socket = io({
  auth: {
    token: "abc"
  }
});

// or with a function
const socket = io({
  auth: (cb) => {
    cb({
      token: "abc"
    });
  }
});
```

可以在服务器端的[握手](https://socket.io/zh-CN/docs/v4/server-socket-instance/#sockethandshake)对象中访问这些凭据：

```js
io.use((socket, next) => {
  const token = socket.handshake.auth.token;
  // ...
});
```

## 处理中间件错误

如果`next`使用 Error 对象调用该方法，则连接将被拒绝并且客户端将收到一个`connect_error`事件。

```js
// client-side
socket.on("connect_error", (err) => {
  console.log(err.message); // prints the message associated with the error
});
```

您可以将其他详细信息附加到错误对象：

```js
// server-side
io.use((socket, next) => {
  const err = new Error("not authorized");
  err.data = { content: "Please retry later" }; // additional details
  next(err);
});

// client-side
socket.on("connect_error", (err) => {
  console.log(err instanceof Error); // true
  console.log(err.message); // not authorized
  console.log(err.data); // { content: "Please retry later" }
});
```

## 与Express中间件的兼容性

大多数现有的[Express 中间件](http://expressjs.com/en/resources/middleware.html)模块应该与 Socket.IO 兼容，您只需要一个小包装函数来使方法签名匹配：

```js
const wrap = middleware => (socket, next) => middleware(socket.request, {}, next);
```

复制

结束请求-响应周期并且不调用的中间件函数`next()`将不起作用。

[express-session](https://www.npmjs.com/package/express-session)示例：

```js
const session = require("express-session");

io.use(wrap(session({ secret: "cats" })));

io.on("connection", (socket) => {
  const session = socket.request.session;
});
```

[Passport](http://www.passportjs.org/)示例：

```js
const session = require("express-session");
const passport = require("passport");

io.use(wrap(session({ secret: "cats" })));
io.use(wrap(passport.initialize()));
io.use(wrap(passport.session()));

io.use((socket, next) => {
  if (socket.request.user) {
    next();
  } else {
    next(new Error("unauthorized"))
  }
});
```

可以在[此处](https://github.com/socketio/socket.io/tree/master/examples/passport-example)找到 Passport 的完整示例。

# 反向代理

您将在下面找到在反向代理解决方案后面部署 Socket.IO 服务器所需的配置，例如：

- [NginX](https://socket.io/zh-CN/docs/v4/reverse-proxy/#nginx)
- [Apache HTTPD](https://socket.io/zh-CN/docs/v4/reverse-proxy/#apache-httpd)
- [Node.js `http-proxy`](https://socket.io/zh-CN/docs/v4/reverse-proxy/#nodejs-http-proxy)

在多服务器设置中，请查看[此处](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/)的文档。

## NginX

`/etc/nginx/nginx.conf`内容：

```nginx
http {
  server {
    listen 80;
    server_name example.com;

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $host;

      proxy_pass http://localhost:3000;

      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }
}
```

有关的：

- [proxy_pass 稳定](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)
- [多服务器设置中的配置](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#nginx-configuration)

如果您只想转发 Socket.IO 请求（例如当 NginX 处理静态内容时）：

```text
http {
  server {
    listen 80;
    root /var/www/html;

    location /socket.io/ {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $host;

      proxy_pass http://localhost:3000;

      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }
}
```

或使用自定义[路径](https://socket.io/zh-CN/docs/v4/server-options/#path):

```text
http {
  server {
    listen 80;
    root /var/www/html;

    location /my-custom-path/ {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $host;

      proxy_pass http://localhost:3000;

      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }
}
```

在这种情况下，必须相应地配置服务器和客户端：

*服务器*

```js
import { Server } from "socket.io";

const io = new Server({
  path: "/my-custom-path/"
});
```

*客户端*

```js
import { io } from "socket.io-client";

const socket = io({
  path: "/my-custom-path/"
});
```

## Apache HTTPD

`/usr/local/apache2/conf/httpd.conf`内容：

```apache
Listen 80

ServerName example.com

LoadModule mpm_event_module             modules/mod_mpm_event.so

LoadModule authn_file_module            modules/mod_authn_file.so
LoadModule authn_core_module            modules/mod_authn_core.so
LoadModule authz_host_module            modules/mod_authz_host.so
LoadModule authz_groupfile_module       modules/mod_authz_groupfile.so
LoadModule authz_user_module            modules/mod_authz_user.so
LoadModule authz_core_module            modules/mod_authz_core.so

LoadModule headers_module               modules/mod_headers.so
LoadModule lbmethod_byrequests_module   modules/mod_lbmethod_byrequests.so
LoadModule proxy_module                 modules/mod_proxy.so
LoadModule proxy_balancer_module        modules/mod_proxy_balancer.so
LoadModule proxy_http_module            modules/mod_proxy_http.so
LoadModule proxy_wstunnel_module        modules/mod_proxy_wstunnel.so
LoadModule rewrite_module               modules/mod_rewrite.so
LoadModule slotmem_shm_module           modules/mod_slotmem_shm.so
LoadModule unixd_module                 modules/mod_unixd.so

User daemon
Group daemon

ProxyPass / http://localhost:3000/
RewriteEngine on
RewriteCond %{HTTP:Upgrade} websocket [NC]
RewriteCond %{HTTP:Connection} upgrade [NC]
RewriteRule ^/?(.*) "ws://localhost:3000/$1" [P,L]

ProxyTimeout 3
```

有关的：

- [mod_proxy_wstunnel 文档](https://httpd.apache.org/docs/2.4/en/mod/mod_proxy_wstunnel.html)
- [多服务器设置中的配置](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#apache-httpd-configuration)

## Node.js `http-proxy`[](https://socket.io/zh-CN/docs/v4/reverse-proxy/#nodejs-http-proxy)

安装： `npm i http-proxy`

```js
const httpProxy = require("http-proxy");

httpProxy
  .createProxyServer({
    target: "http://localhost:3000",
    ws: true,
  })
  .listen(80);
```

复制

[Documentation](https://github.com/http-party/node-http-proxy#readme)

## Caddy 2

[Caddy 2](https://caddyserver.com/v2)中`Caddyfile`的内容

```text
example.com {
  rewrite /path /path/
  handle /path/* {
    uri strip_prefix /path
    rewrite * /socket.io{path}
    reverse_proxy localhost:3000 {
      header_up Host {host}
      header_up X-Real-IP {remote}
    }
  }
}
```

有关的：

- [解决方案论坛帖子](https://caddy.community/t/i-cant-get-socket-io-proxy-to-work-on-v2/8703/2)
- [Caddyfile 指令](https://caddyserver.com/docs/caddyfile/directives)

# 使用多个节点

部署多个 Socket.IO 服务器时，需要注意两件事：

- 如果启用了 HTTP 长轮询（这是默认设置），则启用粘性会话：见[下文](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#enabling-sticky-session)
- 使用兼容的适配器，请参见[此处](https://socket.io/zh-CN/docs/v4/adapter/)

## 粘性负载平衡

如果您计划在不同的进程或机器之间分配连接负载，则必须确保与特定会话 ID 关联的所有请求都到达发起它们的进程。

### 为什么需要粘性会话

这是因为 HTTP 长轮询传输在 Socket.IO 会话的生命周期内发送多个 HTTP 请求。

事实上，Socket.IO 在技术上可以在没有粘性会话的情况下工作，具有以下同步（虚线）：

![Using multiple nodes without sticky sessions](https://socket.io/zh-CN/assets/images/mutiple-nodes-no-sticky-babd7860f217e09eefc2db73e5012f91.png)

虽然显然可以实现，但我们认为 Socket.IO 服务器之间的这种同步过程会对您的应用程序造成很大的性能影响。

评论：

- 如果不启用粘性会话，由于“会话 ID 未知”，您将遇到 HTTP 400 错误
- WebSocket 传输没有这个限制，因为它依赖于整个会话的单个 TCP 连接。这意味着如果您禁用 HTTP 长轮询传输，您将不需要粘性会话：

```js
const socket = io("https://io.yourhost.com", {
  // WARNING: in that case, there is no fallback to long-polling
  transports: [ "websocket" ] // or [ "websocket", "polling" ] (the order matters)
});
```

文档：[`transports`](https://socket.io/zh-CN/docs/v4/client-options/#transports)

### 启用粘性会话的方式

要实现粘性会话，主要有两种解决方案：

- 基于 cookie 路由客户端（推荐解决方案）
- 根据客户端的原始地址路由客户端

您将在下面找到一些常见负载平衡解决方案的示例：

- [NginX](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#nginx-configuration) (基于IP)
- [Apache HTTPD](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#apache-httpd-configuration) (基于cookie)
- [HAProxy](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#haproxy-configuration) (基于cookie)
- [Traefik](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#traefik) (基于cookie)
- [Node.js `cluster` module](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#using-nodejs-cluster)

**重要提示**：如果您处于 CORS 情况（前端域与服务器域不同）并且会话亲和性是通过 cookie 实现的，则需要允许凭据：

*服务器*

```js
const io = require("socket.io")(httpServer, {
  cors: {
    origin: "https://front-domain.com",
    methods: ["GET", "POST"],
    credentials: true
  }
});
```

*客户端*

```js
const io = require("socket.io-client");
const socket = io("https://server-domain.com", {
  withCredentials: true
});
```

没有它，浏览器将不会发送 cookie，您将遇到 HTTP 400“会话 ID 未知”响应。更多信息[在这里](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/withCredentials).

### NginX 配置

在文件的`http { }`部分中`nginx.conf`，您可以声明一个`upstream`包含要平衡负载的 Socket.IO 进程列表的部分：

```nginx
http {
  server {
    listen 3000;
    server_name io.yourhost.com;

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $host;

      proxy_pass http://nodes;

      # enable WebSockets
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }

  upstream nodes {
    # enable sticky session with either "hash" (uses the complete IP address)
    hash $remote_addr consistent;
    # or "ip_hash" (uses the first three octets of the client IPv4 address, or the entire IPv6 address)
    # ip_hash;
    # or "sticky" (needs commercial subscription)
    # sticky cookie srv_id expires=1h domain=.example.com path=/;

    server app01:3000;
    server app02:3000;
    server app03:3000;
  }
}
```

请注意`hash`指示连接将是粘性的说明。

确保您还在`worker_processes`最顶层配置以指示 NginX 应该使用多少工作人员。您可能还想研究调整块`worker_connections`内的设置`events { }`。

### Apache HTTPD 配置[](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/#apache-httpd-configuration)

```apache
Header add Set-Cookie "SERVERID=sticky.%{BALANCER_WORKER_ROUTE}e; path=/" env=BALANCER_ROUTE_CHANGED

<Proxy "balancer://nodes_polling">
    BalancerMember "http://app01:3000" route=app01
    BalancerMember "http://app02:3000" route=app02
    BalancerMember "http://app03:3000" route=app03
    ProxySet stickysession=SERVERID
</Proxy>

<Proxy "balancer://nodes_ws">
    BalancerMember "ws://app01:3000" route=app01
    BalancerMember "ws://app02:3000" route=app02
    BalancerMember "ws://app03:3000" route=app03
    ProxySet stickysession=SERVERID
</Proxy>

RewriteEngine On
RewriteCond %{HTTP:Upgrade} =websocket [NC]
RewriteRule /(.*) balancer://nodes_ws/$1 [P,L]
RewriteCond %{HTTP:Upgrade} !=websocket [NC]
RewriteRule /(.*) balancer://nodes_polling/$1 [P,L]

ProxyTimeout 3
```

链接：

- [例子](https://github.com/socketio/socket.io/tree/master/examples/cluster-httpd)
- [文档](https://httpd.apache.org/docs/2.4/en/mod/mod_proxy.html#proxypass)

### HAProxy 配置

```text
# Reference: http://blog.haproxy.com/2012/11/07/websockets-load-balancing-with-haproxy/

listen chat
  bind *:80
  default_backend nodes

backend nodes
  option httpchk HEAD /health
  http-check expect status 200
  cookie io prefix indirect nocache # using the `io` cookie set upon handshake
  server app01 app01:3000 check cookie app01
  server app02 app02:3000 check cookie app02
  server app03 app03:3000 check cookie app03
```

链接：

- [例子](https://github.com/socketio/socket.io/tree/master/examples/cluster-haproxy)
- [文档](http://cbonte.github.io/haproxy-dconv/2.4/configuration.html#cookie)

### Traefik

使用容器标签：

```yaml
# docker-compose.yml
services:
  traefik:
    image: traefik:2.4
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    links:
      - server

  server:
    image: my-image:latest
    labels:
      - "traefik.http.routers.my-service.rule=PathPrefix(`/`)"
      - traefik.http.services.my-service.loadBalancer.sticky.cookie.name=server_id
      - traefik.http.services.my-service.loadBalancer.sticky.cookie.httpOnly=true
```

使用[文件提供程序](https://doc.traefik.io/traefik/v2.0/providers/file/)：

```yaml
## Dynamic configuration
http:
  services:
    my-service:
      rule: "PathPrefix(`/`)"
      loadBalancer:
        sticky:
          cookie:
            name: server_id
            httpOnly: true
```

链接：

- [例子](https://github.com/socketio/socket.io/tree/master/examples/cluster-traefik)
- [文档](https://doc.traefik.io/traefik/v2.0/routing/services/#sticky-sessions)

### 使用 Node.js 集群

就像 NginX 一样，Node.js 通过`cluster`模块提供了内置的集群支持。

有几种解决方案，具体取决于您的用例：

| NPM 包                                                       | 这个怎么运作                         |
| ------------------------------------------------------------ | ------------------------------------ |
| [`@socket.io/sticky`](https://github.com/darrachequesne/socket.io-sticky) | 路由基于`sid`查询参数                |
| [`sticky-session`](https://github.com/indutny/sticky-session) | 路由是基于`connection.remoteAddress` |
| [`socketio-sticky-session`](https://github.com/wzrdtales/socket-io-sticky-session) | 基于`x-forwarded-for`报头的路由）    |

示例`@socket.io/sticky`:

```js
const cluster = require("cluster");
const http = require("http");
const { Server } = require("socket.io");
const numCPUs = require("os").cpus().length;
const { setupMaster, setupWorker } = require("@socket.io/sticky");
const { createAdapter, setupPrimary } = require("@socket.io/cluster-adapter");

if (cluster.isMaster) {
  console.log(`Master ${process.pid} is running`);

  const httpServer = http.createServer();

  // setup sticky sessions
  setupMaster(httpServer, {
    loadBalancingMethod: "least-connection",
  });

  // setup connections between the workers
  setupPrimary();

  // needed for packets containing buffers (you can ignore it if you only send plaintext objects)
  // Node.js < 16.0.0
  cluster.setupMaster({
    serialization: "advanced",
  });
  // Node.js > 16.0.0
  // cluster.setupPrimary({
  //   serialization: "advanced",
  // });

  httpServer.listen(3000);

  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on("exit", (worker) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork();
  });
} else {
  console.log(`Worker ${process.pid} started`);

  const httpServer = http.createServer();
  const io = new Server(httpServer);

  // use the cluster adapter
  io.adapter(createAdapter());

  // setup connection with the primary process
  setupWorker(io);

  io.on("connection", (socket) => {
    /* ... */
  });
}
```

## 在节点之间传递事件

既然您有多个接受连接的Socket.IO 节点，如果您想向所有客户端（或某个[房间](https://socket.io/zh-CN/docs/v4/rooms/)中的客户端）广播事件，您将需要某种方式在进程或计算机之间传递消息。

负责路由消息的接口就是我们所说的[Adapter](https://socket.io/zh-CN/docs/v4/adapter/)。

# 处理 CORS

从 Socket.IO v3 开始，您需要显式启用[跨域资源共享](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)(CORS)。

```js
import { createServer } from "http";
import { Server } from "socket.io";

const httpServer = createServer();
const io = new Server(httpServer, {
  cors: {
    origin: "https://example.com"
  }
});
```

所有选项都将转发到[cors](https://www.npmjs.com/package/cors)包。可以在[此处](https://github.com/expressjs/cors#configuration-options)找到完整的选项列表。

带有 cookie ( [withCredentials](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/withCredentials)) 和附加标头的示例：

```js
// server-side
const io = new Server(httpServer, {
  cors: {
    origin: "https://example.com",
    allowedHeaders: ["my-custom-header"],
    credentials: true
  }
});

// client-side
import { io } from "socket.io-client";
const socket = io("https://api.example.com", {
  withCredentials: true,
  extraHeaders: {
    "my-custom-header": "abcd"
  }
});
```

注意：如果您的 Web 应用程序和服务器不是从同一个端口提供服务，这也适用于 localhost

```js
const io = new Server(httpServer, {
  cors: {
    origin: "http://localhost:8080"
  }
});

httpServer.listen(3000);
```

您可以使用以下选项禁止所有跨域请求[`allowRequest`](https://socket.io/zh-CN/docs/v4/server-options/#allowrequest) option:

```js
const io = new Server(httpServer, {
  allowRequest: (req, callback) => {
    const noOriginHeader = req.headers.origin === undefined;
    callback(null, noOriginHeader);
  }
});
```

## 故障排除[](https://socket.io/zh-CN/docs/v4/handling-cors/#troubleshooting)

### 缺少 CORS 标头“Access-Control-Allow-Origin”[](https://socket.io/zh-CN/docs/v4/handling-cors/#cors-header-access-control-allow-origin-missing)

完整的错误信息：

> *跨域请求被阻止：同源策略不允许读取位于 .../socket.io/?EIO=4&transport=polling&t=NMnp2WI 的远程资源。（原因：缺少 CORS 标头“Access-Control-Allow-Origin”）。*

如果您已正确配置您的服务器（见[上文](https://socket.io/zh-CN/docs/v4/handling-cors/#configuration)），这可能意味着您的浏览器无法访问 Socket.IO 服务器。

以下命令：

```text
curl "https://api.example.com/socket.io/?EIO=4&transport=polling"
```

应该返回类似：

```text
0{"sid":"Lbo5JLzTotvW3g2LAAAA","upgrades":["websocket"],"pingInterval":25000,"pingTimeout":20000}
```

如果不是这种情况，请检查您的服务器是否正在侦听并且实际上可以在给定端口上访问。

### 如果 CORS 标头“Access-Control-Allow-Origin”为“*”，则不支持凭据[](https://socket.io/zh-CN/docs/v4/handling-cors/#credential-is-not-supported-if-the-cors-header-access-control-allow-origin-is-)

完整的错误信息：

> *跨域请求被阻止：同源策略不允许读取位于“.../socket.io/?EIO=4&transport=polling&t=NvQfU77”的远程资源。（原因：如果 CORS 标头“Access-Control-Allow-Origin”为“\*”，则不支持凭证）*

您不能同时设置[`withCredentials`](https://socket.io/zh-CN/docs/v4/client-options/#withcredentials) 为 `true` 和 `origin: *`，您需要使用特定的来源：

```js
import { createServer } from "http";
import { Server } from "socket.io";

const httpServer = createServer();
const io = new Server(httpServer, {
  cors: {
    origin: "https://my-frontend.com",
    // or with an array of origins
    // origin: ["https://my-frontend.com", "https://my-other-frontend.com", "http://localhost:3000"],
    credentials: true
  }
});
```

### CORS 标头“Access-Control-Allow-Credentials”中预期为“true”[](https://socket.io/zh-CN/docs/v4/handling-cors/#expected-true-in-cors-header-access-control-allow-credentials)

完整的错误信息：

> *跨域请求被阻止：同源策略不允许读取位于 .../socket.io/?EIO=4&transport=polling&t=NvQny19 的远程资源。（原因：CORS 标头“Access-Control-Allow-Credentials”中预期为“true”）*

在这种情况下，在客户端上[`withCredentials`](https://socket.io/zh-CN/docs/v4/client-options/#withcredentials)设置为`true`，但服务器缺少选项`credentials`中的属性[`cors`](https://socket.io/zh-CN/docs/v4/server-options/#cors) 。请参见上面的示例。

# 客户端初始化

在下面的示例中，`io`对象来自：

- 使用 `<script>` 引入

```html
<script src="/socket.io/socket.io.js"></script>
```

复制

- 使用 ESM 引入

```html
<script type="module">
  import { io } from "https://cdn.socket.io/4.3.2/socket.io.esm.min.js";
</script>
```

复制

- NPM

```js
import { io } from "socket.io-client";
```

## 来自同一域[](https://socket.io/zh-CN/docs/v4/client-initialization/#from-the-same-domain)

如果您的前端与您的服务器在同一个域上提供服务，您可以简单地使用：

```js
const socket = io();
```

服务器 URL 将从 [window.location](https://developer.mozilla.org/en-US/docs/Web/API/Window/location)对象中推导出来

## 来自不同的域[](https://socket.io/zh-CN/docs/v4/client-initialization/#from-a-different-domain)

如果您的前端不是来自与服务器相同的域，则必须传递服务器的 URL。

```js
const socket = io("https://server-domain.com");
```

在这种情况下，请确保在服务器上启用 [跨域资源共享 (CORS)](https://socket.io/zh-CN/docs/v4/handling-cors/)。

##### 信息

您可以使用`https` 或 `wss` (分别为, `http` 或 `ws`).

```js
// the following forms are similar
const socket = io("https://server-domain.com");
const socket = io("wss://server-domain.com");
const socket = io("server-domain.com"); // only in the browser when the page is served over https (will not work in Node.js)
```

## 自定义命名空间[](https://socket.io/zh-CN/docs/v4/client-initialization/#custom-namespace)

在上面的示例中，客户端将连接到主命名空间。对于大多数用例来说，仅使用主命名空间就足够了，但您可以使用以下命令指定命名空间：

```js
// same origin version
const socket = io("/admin");
// cross origin version
const socket = io("https://server-domain.com/admin");
```

您可以[在此处](https://socket.io/zh-CN/docs/v4/namespaces/)找到有关名称空间的更多详细信息。

## 配置[](https://socket.io/zh-CN/docs/v4/client-initialization/#options)

可在[此处](https://socket.io/zh-CN/docs/v4/client-options/)找到可用配置的完整列表

# Socket 实例（客户端）

`Socket`是与服务器交互的基础类。它继承了 Node.js[EventEmitter](https://nodejs.org/api/events.html#class-eventemitter)的大部分方法，例如 [emit](https://socket.io/zh-CN/docs/v4/client-api/#socketemiteventname-args), [on](https://socket.io/zh-CN/docs/v4/client-api/#socketoneventname-callback), [once](https://socket.io/zh-CN/docs/v4/client-api/#socketonceeventname-callback) 或 [off](https://socket.io/zh-CN/docs/v4/client-api/#socketoffeventname)。

![Bidirectional communication between server and client](https://socket.io/zh-CN/images/bidirectional-communication-socket.png)



除了[emitting](https://socket.io/zh-CN/docs/v4/emitting-events/) 和 [listening to](https://socket.io/zh-CN/docs/v4/listening-to-events/)事件之外，Socket 实例还有一些可能在您的应用程序中使用的属性：

## Socket#id[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#socketid)

每个新连接都分配有一个随机的 20 个字符的标识符。

此标识符与服务器端的值同步。

```js
// server-side
io.on("connection", (socket) => {
  console.log(socket.id); // x8WIv7-mJelg7on_ALbx
});

// client-side
socket.on("connect", () => {
  console.log(socket.id); // x8WIv7-mJelg7on_ALbx
});

socket.on("disconnect", () => {
  console.log(socket.id); // undefined
});
```

## Socket#connected[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#socketconnected)

该属性描述套接字当前是否连接到服务器。

```js
socket.on("connect", () => {
  console.log(socket.connected); // true
});

socket.on("disconnect", () => {
  console.log(socket.connected); // false
});
```

## Socket#io[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#socketio)

对基础[Manager](https://socket.io/zh-CN/docs/v4/client-api/#manager)的引用。

```js
socket.on("connect", () => {
  const engine = socket.io.engine;
  console.log(engine.transport.name); // in most cases, prints "polling"

  engine.once("upgrade", () => {
    // called when the transport is upgraded (i.e. from HTTP long-polling to WebSocket)
    console.log(engine.transport.name); // in most cases, prints "websocket"
  });

  engine.on("packet", ({ type, data }) => {
    // called for each packet received
  });

  engine.on("packetCreate", ({ type, data }) => {
    // called for each packet sent
  });

  engine.on("drain", () => {
    // called when the write buffer is drained
  });

  engine.on("close", (reason) => {
    // called when the underlying connection is closed
  });
});
```

## 生命周期[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#lifecycle)

![Lifecycle diagram](https://socket.io/images/client_socket_events.png)

## 事件[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#events)

Socket 实例发出三个特殊事件：

- [`connect`](https://socket.io/zh-CN/docs/v4/client-socket-instance/#connect)
- [`connect_error`](https://socket.io/zh-CN/docs/v4/client-socket-instance/#connect-error)
- [`disconnect`](https://socket.io/zh-CN/docs/v4/client-socket-instance/#disconnect)

请注意，从 Socket.IO v3 开始，Socket 实例不再发出任何与重新连接逻辑相关的事件。您可以直接监听 Manager 实例上的事件：

```js
socket.io.on("reconnect_attempt", () => {
  // ...
});

socket.io.on("reconnect", () => {
  // ...
});
```

更多信息可以在[迁移指南](https://socket.io/zh-CN/docs/v4/migrating-from-2-x-to-3-0/#the-socket-instance-will-no-longer-forward-the-events-emitted-by-its-manager)中找到

### `connect`[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#connect)

此事件由 Socket 实例在连接**和**重新连接时触发。

```js
socket.on("connect", () => {
  // ...
});
```

请注意，您不应在`connect`处理程序本身中注册事件处理程序，因为每次 Socket 重新连接时都会注册一个新的处理程序：

```js
// BAD
socket.on("connect", () => {
  socket.on("data", () => { /* ... */ });
});

// GOOD
socket.on("connect", () => {
  // ...
});

socket.on("data", () => { /* ... */ });
```

### `connect_error`[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#connect_error)

在以下情况下触发此事件：

- 低级连接无法建立
- 服务器在[中间件功能](https://socket.io/zh-CN/docs/v4/middlewares/)中拒绝连接

在第一种情况下，Socket 会在 [给定的延迟](https://socket.io/zh-CN/docs/v4/client-options/#reconnectiondelay)之后自动尝试重新连接。

在后一种情况下，您需要手动重新连接。您可能需要更新凭据：

```js
// either by directly modifying the `auth` attribute
socket.on("connect_error", () => {
  socket.auth.token = "abcd";
  socket.connect();
});

// or if the `auth` attribute is a function
const socket = io({
  auth: (cb) => {
    cb(localStorage.getItem("token"));
  }
});

socket.on("connect_error", () => {
  setTimeout(() => {
    socket.connect();
  }, 1000);
});
```

### `disconnect`[](https://socket.io/zh-CN/docs/v4/client-socket-instance/#disconnect)

此事件在断开连接时触发。

```js
socket.on("disconnect", (reason) => {
  // ...
});
```

复制

以下是可能的原因列表：

| Reason                 | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `io server disconnect` | 服务器已使用[socket.disconnect()](https://socket.io/zh-CN/docs/v4/server-api/#socketdisconnectclose)强制断开socket |
| `io client disconnect` | 使用[socket.disconnect()](https://socket.io/zh-CN/docs/v4/client-api/#socketdisconnect)手动断开socket |
| `ping timeout`         | 服务器未在该`pingInterval + pingTimeout`范围内发送 PING      |
| `transport close`      | 连接已关闭（例如：用户失去连接，或网络从 WiFi 更改为 4G）    |
| `transport error`      | 连接遇到错误（例如：服务器在 HTTP 长轮询周期中被杀死）       |

前两种情况（显式断开），客户端不会尝试重新连接，需要手动调用`socket.connect()`.

在所有其他情况下，客户端将等待一个小的[随机延迟](https://socket.io/zh-CN/docs/v4/client-options/#reconnectiondelay)，然后尝试重新连接：

```js
socket.on("disconnect", (reason) => {
  if (reason === "io server disconnect") {
    // the disconnection was initiated by the server, you need to reconnect manually
    socket.connect();//服务器断开的，需要手动执行重连
  }
  // else the socket will automatically try to reconnect
});
```

注意：这些事件以及`disconnecting`, `newListener` 和 `removeListener`是不应在您的应用程序中使用的特殊事件：

```js
// BAD, will throw an error
socket.emit("disconnect");
```

# 离线行为

## 缓冲事件[](https://socket.io/zh-CN/docs/v4/client-offline-behavior/#buffered-events)

默认情况下，在 Socket 未连接时发出的任何事件都将被缓冲，直到重新连接。

虽然在大多数情况下很有用（当重新连接延迟很短时），但它可能会在连接恢复时导致大量事件。

有几种解决方案可以防止这种行为，具体取决于您的用例：

- 使用Socket 实例的[connected 属性](https://socket.io/zh-CN/docs/v4/client-socket-instance/#socketconnected) attribute of the Socket instance

```js
if (socket.connected) {
  socket.emit( /* ... */ );
} else {
  // ...
}
```

- 使用 [volatile 事件](https://socket.io/zh-CN/docs/v4/emitting-events/#volatile-events)

```js
socket.volatile.emit( /* ... */ );
```

# 发送事件

有几种方法可以在服务器和客户端之间发送事件。

## 基本的 emit[](https://socket.io/zh-CN/docs/v4/emitting-events/#基本的-emit)

Socket.IO API 的灵感来自 Node.js [EventEmitter](https://nodejs.org/docs/latest/api/events.html#events_events)，这意味着您可以在一侧发出事件并在另一侧注册侦听器：

*服务器*

```js
io.on("connection", (socket) => {
  socket.emit("hello", "world");
});
```

*客户端*

```js
socket.on("hello", (arg) => {
  console.log(arg); // world
});
```

这也适用于另一个方向：

*服务器*

```js
io.on("connection", (socket) => {
  socket.on("hello", (arg) => {
    console.log(arg); // world
  });
});
```

*客户端*

```js
socket.emit("hello", "world");
```

您可以发送任意数量的参数，并且支持所有可序列化的数据结构，包括像[Buffer](https://nodejs.org/docs/latest/api/buffer.html#buffer_buffer) 或 [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)这样的二进制对象。

*服务器*

```js
io.on("connection", (socket) => {
  socket.emit("hello", 1, "2", { 3: '4', 5: Buffer.from([6]) });
});
```

*客户端*

```js
// client-side
socket.on("hello", (arg1, arg2, arg3) => {
  console.log(arg1); // 1
  console.log(arg2); // "2"
  console.log(arg3); // { 3: '4', 5: ArrayBuffer (1) [ 6 ] }
});
```

无需`JSON.stringify()`，因为它会为您完成。

```js
// BAD
socket.emit("hello", JSON.stringify({ name: "John" }));

// GOOD
socket.emit("hello", { name: "John" });
```

笔记：

- [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)对象将被转换为（并作为）它们的字符串表示形式，例如`1970-01-01T00:00:00.000Z`
- [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 和 [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)必须手动序列化：

```js
const serializedMap = [...myMap.entries()];
const serializedSet = [...mySet.keys()];
```

- 您可以使用该[`toJSON()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#tojson_behavior)方法自定义对象的序列化

一个类的例子：

```js
class Hero {
  #hp;

  constructor() {
    this.#hp = 42;
  }

  toJSON() {
    return { hp: this.#hp };
  }
}

socket.emit("here's a hero", new Hero());
```

## 回调[](https://socket.io/zh-CN/docs/v4/emitting-events/#回调)

事件很棒，但在某些情况下，您可能需要更经典的请求-响应 API。在 Socket.IO 中，此功能称为确认。

您可以添加一个回调作为`emit()`的最后一个参数，一旦对方确认事件，就会调用此回调：

*服务器*

```js
io.on("connection", (socket) => {
  socket.on("update item", (arg1, arg2, callback) => {
    console.log(arg1); // 1
    console.log(arg2); // { name: "updated" }
    callback({
      status: "ok"
    });
  });
});
```

*客户端*

```js
socket.emit("update item", "1", { name: "updated" }, (response) => {
  console.log(response.status); // ok
});
```

## 超时[](https://socket.io/zh-CN/docs/v4/emitting-events/#超时)

从 Socket.IO v4.4.0 开始，您现在可以为每个发射分配超时：

```js
socket.timeout(5000).emit("my-event", (err) => {
  if (err) {
    // the other side did not acknowledge the event in the given delay
  }
});
```

You can also use both a timeout and an [acknowledgement](https://socket.io/zh-CN/docs/v4/emitting-events/#acknowledgements):

```js
socket.timeout(5000).emit("my-event", (err, response) => {
  if (err) {
    // the other side did not acknowledge the event in the given delay
  } else {
    console.log(response);
  }
});
```

## 易失性事件[](https://socket.io/zh-CN/docs/v4/emitting-events/#易失性事件)

易失性事件是在底层连接未准备好时不会发送的事件（有点像[UDP](https://fr.wikipedia.org/wiki/User_Datagram_Protocol)，在可靠性方面）。

例如，如果您需要发送在线游戏中角色的位置（因为只有最新的值才有用），这可能会很有趣。

```js
socket.volatile.emit("hello", "might or might not be received");
```

另一个用例是在客户端未连接时丢弃事件（默认情况下，事件会被缓冲直到重新连接）。

例子：

*服务器*

```js
io.on("connection", (socket) => {
  console.log("connect");

  socket.on("ping", (count) => {
    console.log(count);
  });
});
```

*客户端*

```js
let count = 0;
setInterval(() => {
  socket.volatile.emit("ping", ++count);
}, 1000);
```

如果重新启动服务器，您将在控制台中看到：

```text
connect
1
2
3
4
# the server is restarted, the client automatically reconnects
connect
9
10
11
```



如果没有`volatile`标志，您将看到：

```text
connect
1
2
3
4
# the server is restarted, the client automatically reconnects and sends its buffered events
connect
5
6
7
8
9
10
11
```

# 监听事件

有几种方法可以处理在服务器和客户端之间传输的事件。

## EventEmitter 方法[](https://socket.io/zh-CN/docs/v4/listening-to-events/#eventemitter-methods)

在服务器端，Socket 实例扩展了 Node.js [EventEmitter](https://nodejs.org/docs/latest/api/events.html#events_events)类。

在客户端，Socket 实例使用[component-emitter](https://github.com/component/emitter)库提供的事件发射器，它公开了 EventEmitter 方法的子集。

### socket.on(eventName, listener)[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketoneventname-listener)

将*侦听器*函数添加到名为*eventName*的事件的侦听器数组的末尾。

```js
socket.on("details", (...args) => {
  // ...
});
```



### socket.once(eventName, listener)[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketonceeventname-listener)

为名为*eventName*的事件添加**一次性**监听函数

```js
socket.once("details", (...args) => {
  // ...
});
```



### socket.off(eventName, listener)[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketoffeventname-listener)

从名为*eventName*的事件的侦听器数组中移除指定的*侦听器*。

```js
const listener = (...args) => {
  console.log(args);
}

socket.on("details", listener);

// and then later...
socket.off("details", listener);
```



### socket.removeAllListeners([eventName])[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketremovealllistenerseventname)

删除所有侦听器，或指定*eventName*的侦听器。

```js
// for a specific event
socket.removeAllListeners("details");
// for all events
socket.removeAllListeners();
```



## Catch-all 侦听器[](https://socket.io/zh-CN/docs/v4/listening-to-events/#catch-all-listeners)

从 Socket.IO v3 开始，受[EventEmitter2](https://github.com/EventEmitter2/EventEmitter2)库启发的新 API 允许声明 Catch-all 侦听器。

此功能在客户端和服务器上均可用。

### socket.onAny(listener)[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketonanylistener)

添加一个监听器，当任何事件发出时将被触发。

```js
socket.onAny((eventName, ...args) => {
  // ...
});
```



### socket.prependAny(listener)[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketprependanylistener)

添加一个监听器，当任何事件发出时将被触发。侦听器被添加到侦听器数组的开头。

```js
socket.prependAny((eventName, ...args) => {
  // ...
});
```



### socket.offAny([listener])[](https://socket.io/zh-CN/docs/v4/listening-to-events/#socketoffanylistener)

删除所有catch-all侦听器或给定的侦听器。

```js
const listener = (eventName, ...args) => {
  console.log(eventName, args);
}

socket.onAny(listener);

// and then later...
socket.offAny(listener);

// or all listeners
socket.offAny();
```



## 验证[](https://socket.io/zh-CN/docs/v4/listening-to-events/#validation)

事件参数的验证超出了 Socket.IO 库的范围。

JS 生态系统中有许多包涵盖了这个用例，其中包括：

- [joi](https://www.npmjs.com/package/joi)
- [ajv](https://www.npmjs.com/package/ajv)
- [validatorjs](https://www.npmjs.com/package/validatorjs)

带有[joi](https://joi.dev/api/)和[acknowledgements](https://socket.io/zh-CN/docs/v4/emitting-events/#acknowledgements)的示例：

```js
const Joi = require("joi");

const userSchema = Joi.object({
  username: Joi.string().max(30).required(),
  email: Joi.string().email().required()
});

io.on("connection", (socket) => {
  socket.on("create user", (payload, callback) => {
    if (typeof callback !== "function") {
      // not an acknowledgement
      return socket.disconnect();
    }
    const { error, value } = userSchema.validate(payload);
    if (error) {
      return callback({
        status: "KO",
        error
      });
    }
    // do something with the value, and then
    callback({
      status: "OK"
    });
  });

});
```



## 错误处理[](https://socket.io/zh-CN/docs/v4/listening-to-events/#error-handling)

Socket.IO 库中目前没有内置的错误处理，这意味着您必须捕获任何可能在侦听器中引发的错误。

```js
io.on("connection", (socket) => {
  socket.on("list items", async (callback) => {
    try {
      const items = await findItems();
      callback({
        status: "OK",
        items
      });
    } catch (e) {
      callback({
        status: "NOK"
      });
    }
  });
});
```



在服务器端，使用`EventEmitter.captureRejections = true`（实验性，请参见[此处](https://nodejs.org/api/events.html#events_capture_rejections_of_promises)）也可能很有趣，具体取决于您的用例。

```js
require("events").captureRejections = true;

io.on("connection", (socket) => {
  socket.on("list products", async () => {
    const products = await findProducts();
    socket.emit("products", products);
  });

  socket[Symbol.for('nodejs.rejection')] = (err) => {
    socket.emit("error", err);
  };
});
```

# 广播事件

Socket.IO 使向所有连接的客户端发送事件变得容易。

##### INFO

请注意，广播是**仅服务器**功能。

## 给所有连接的客户端[](https://socket.io/zh-CN/docs/v4/broadcasting-events/#to-all-connected-clients)

![Broadcasting to all connected clients](https://socket.io/zh-CN/images/broadcasting.png)

```js
io.emit("hello", "world");
```



##### CAUTION

当前断开连接（或正在重新连接）的客户端将不会收到该事件。将此事件存储在某处（例如在数据库中）取决于您的用例。

## 除发送者外的所有连接的客户端[](https://socket.io/zh-CN/docs/v4/broadcasting-events/#to-all-connected-clients-except-the-sender)

![Broadcasting to all connected clients excepting the sender](https://socket.io/zh-CN/images/broadcasting2.png)

```js
io.on("connection", (socket) => {
  socket.broadcast.emit("hello", "world");
});
```

##### NOTE

在上面的示例中，使用`socket.emit("hello", "world")`（不带`broadcast`标志）会将事件发送到“客户端 A”。您可以在[备忘单](https://socket.io/zh-CN/docs/v4/emit-cheatsheet/)中找到发送事件的所有方式的列表。

## 使用多个 Socket.IO 服务器[](https://socket.io/zh-CN/docs/v4/broadcasting-events/#with-multiple-socketio-servers)

广播也适用于多个 Socket.IO 服务器。

您只需将默认适配器替换为[Redis 适配器](https://socket.io/zh-CN/docs/v4/adapter/)或其他[兼容的适配器](https://socket.io/zh-CN/docs/v4/redis-adapter/)。

![Broadcasting with Redis](https://socket.io/zh-CN/images/broadcasting-redis.png)

在某些情况下，您可能只想向连接到当前服务器的客户端广播。您可以使用`local`标志实现此目的：

```js
io.local.emit("hello", "world");
```

复制

![Broadcasting with Redis but local](https://socket.io/zh-CN/images/broadcasting-redis-local.png)

为了在广播时针对特定客户，请参阅有关[Rooms](https://socket.io/zh-CN/docs/v4/rooms/)的文档。

# 房间

sockets可以`join` 和 `leave`*房间*。它可用于向一部分客户端广播事件：

![Broadcasting to all clients in a room](https://socket.io/zh-CN/images/rooms.png)

##### 信息

请注意，房间是一个**仅限服务器**的概念（即客户端无权访问它已加入的房间列表）。

## 加入和离开[](https://socket.io/zh-CN/docs/v4/rooms/#joining-and-leaving)

您可以调用`join`以将socket订阅到给定的频道：

```js
io.on("connection", (socket) => {
  socket.join("some room");
});
```



然后在广播或发射时简单地使用`to` 或 `in`（它们是相同的）：

```js
io.to("some room").emit("some event");
```



您可以同时发射到多个房间：

```js
io.to("room1").to("room2").to("room3").emit("some event");
```



在这种情况下，将执行[联合](https://en.wikipedia.org/wiki/Union_(set_theory))：至少在其中一个房间中的每个socket都将获得**一次**事件（即使socket在两个或更多房间中）。

您还可以从给定的socket广播到房间：

```js
io.on("connection", (socket) => {
  socket.to("some room").emit("some event");
});
```



在这种情况下，房间中**除**发送者之外的每个socket都会收到该事件。

![Broadcasting to all clients in a room excepting the sender](https://socket.io/zh-CN/images/rooms2.png)

要离开频道，您调用`leave`的方式与`join`相同。

## 默认房间[](https://socket.io/zh-CN/docs/v4/rooms/#default-room)

Socket.IO 中的每一个`socket`都由一个随机的、不可猜测的、唯一的标识符[Socket#id](https://socket.io/zh-CN/docs/v4/server-socket-instance/#socketid)。为了您的方便，每个socket都会自动加入一个由其自己的 id 标识的房间。

这使得实现私人消息变得容易：

```js
io.on("connection", (socket) => {
  socket.on("private message", (anotherSocketId, msg) => {
    socket.to(anotherSocketId).emit("private message", socket.id, msg);
  });
});
```



## 示例用例[](https://socket.io/zh-CN/docs/v4/rooms/#sample-use-cases)

- 向给定用户的每个设备/选项卡广播数据

```js
io.on("connection", async (socket) => {
  const userId = await fetchUserId(socket);

  socket.join(userId);

  // and then later
  io.to(userId).emit("hi");
});
```

复制

- 发送有关给定实体的通知

```js
io.on("connection", async (socket) => {
  const projects = await fetchProjects(socket);

  projects.forEach(project => socket.join("project:" + project.id));

  // and then later
  io.to("project:4321").emit("project updated");
});
```



## 断开[](https://socket.io/zh-CN/docs/v4/rooms/#disconnection)

断开连接后，`leave`会自动将它们所属的所有通道连接起来，您不需要进行特殊的拆卸。

您可以通过监听`disconnecting`事件来获取 Socket 所在的房间：

```js
io.on("connection", socket => {
  socket.on("disconnecting", () => {
    console.log(socket.rooms); // the Set contains at least the socket ID
  });

  socket.on("disconnect", () => {
    // socket.rooms.size === 0
  });
});
```



## 使用多个 Socket.IO 服务器[](https://socket.io/zh-CN/docs/v4/rooms/#with-multiple-socketio-servers)

与[全局广播](https://socket.io/zh-CN/docs/v4/broadcasting-events/#with-multiple-socketio-servers)一样，向房间广播也适用于多个 Socket.IO 服务器。

您只需要将默认的[Adapter](https://socket.io/zh-CN/docs/v4/glossary/#adapter)替换为 Redis Adapter。更多关于它的信息在[这里](https://socket.io/zh-CN/docs/v4/redis-adapter/)。

![Broadcasting to all clients in a room with Redis](https://socket.io/zh-CN/images/rooms-redis.png)

## 实施细节[](https://socket.io/zh-CN/docs/v4/rooms/#implementation-details)

“房间”功能由我们称为适配器的东西实现。该适配器是一个服务器端组件，负责：

- 存储 Socket 实例和房间之间的关系
- 向所有（或部分）客户端广播事件

您可以在[此处](https://github.com/socketio/socket.io-adapter)找到默认内存适配器的代码。

基本上，它包含两个[ES6 Maps](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map):

- `sids`: `Map<SocketId, Set<Room>>`
- `rooms`: `Map<Room, Set<SocketId>>`

调用`socket.join("the-room")`将导致：

- 在`sids` Map中，将“the-room”添加到由Socket ID 标识的 Set
- 在`rooms` Map 中，将Socket ID 添加到由字符串“the-room”标识的 Set 中

然后在广播时使用这两个地图：

- 对所有套接字的广播（`io.emit()`）循环通过`sids`Map，并将数据包发送到所有sockets
- 对给定房间的广播 ( `io.to("room21").emit()`）循环通过`rooms`Map 中的 Set，并将数据包发送到所有匹配的sockets

您可以通过以下方式访问这些对象：

```js
// main namespace
const rooms = io.of("/").adapter.rooms;
const sids = io.of("/").adapter.sids;

// custom namespace
const rooms = io.of("/my-namespace").adapter.rooms;
const sids = io.of("/my-namespace").adapter.sids;
```



笔记：

- 这些对象并不意味着直接修改，您应该始终使用[`socket.join(...)`](https://socket.io/zh-CN/docs/v4/server-api/#socketjoinroom) 和 [`socket.leave(...)`](https://socket.io/zh-CN/docs/v4/server-api/#socketleaveroom)来代替。
- 在[多服务器](https://socket.io/zh-CN/docs/v4/using-multiple-nodes/)设置中，`rooms` 和 `sids`对象不会在 Socket.IO 服务器之间共享（房间可能只“存在”在一个服务器上而不是另一个服务器上）。

## 房间事件[](https://socket.io/zh-CN/docs/v4/rooms/#room-events)

从`socket.io@3.1.0`开始，底层适配器将发出以下事件：

- `create-room` (argument: room)
- `delete-room` (argument: room)
- `join-room` (argument: room, id)
- `leave-room` (argument: room, id)

例子：

```js
io.of("/").adapter.on("create-room", (room) => {
  console.log(`room ${room} was created`);
});

io.of("/").adapter.on("join-room", (room, id) => {
  console.log(`socket ${id} has joined room ${room}`);
});
```

