---
title: electron 安装问题
date: 2022-09-11 00:59:53
tags:
categories:
- [Electron,note]
---

```
$ npm install electron --save-dev
npm ERR! code 1
npm ERR! path C:\Users\10152\Desktop\study\代码碎屑\electron练习\my-electron-app\node_modules\electron
npm ERR! command failed
npm ERR! command C:\WINDOWS\system32\cmd.exe /d /s /c node install.js
npm ERR! RequestError: read ECONNRESET
npm ERR!     at ClientRequest.<anonymous> (C:\Users\10152\Desktop\study\代码碎屑\electron练习\my-electron-app\node_modules\got\source\request-as-event-emitter.js:178:14)
npm ERR!     at Object.onceWrapper (node:events:642:26)
npm ERR!     at ClientRequest.emit (node:events:539:35)
npm ERR!     at ClientRequest.origin.emit (C:\Users\10152\Desktop\study\代码碎屑\electron练习\my-electron-app\node_modules\@szmarczak\http-timer\source\index.js:37:11)
npm ERR!     at TLSSocket.socketErrorListener (node:_http_client:454:9)
npm ERR!     at TLSSocket.emit (node:events:527:28)
npm ERR!     at emitErrorNT (node:internal/streams/destroy:157:8)
npm ERR!     at emitErrorCloseNT (node:internal/streams/destroy:122:3)
npm ERR!     at processTicksAndRejections (node:internal/process/task_queues:83:21)

npm ERR! A complete log of this run can be found in:
npm ERR!     D:\nodejs\node_cache\_logs\2022-09-10T16_55_35_246Z-debug-0.log

```

eletron安装不成功可以设置下源

```
npm config set ELECTRON_MIRROR https://npmmirror.com/mirrors/electron/
```



#### electron 控制台中文乱码问题

```
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "chcp 65001 && electron ."
  },
```

