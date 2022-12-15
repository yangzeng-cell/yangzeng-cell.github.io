---
title: web性能权威指南
date: 2022-12-09 08:17:27
tags:
- [web性能权威指南]
categories:
- [web性能优化]
---

# web性能权威指南

## 网络技术概览

网络通信决定性影响的两个方面：延迟与带宽

延迟：分组从信息源发送到目的地所需要的时间

带宽：逻辑或者物理通信路径最大的吞吐量

**延迟的构成**

传播延迟：消息从发送端到接收端所需要的时间，是信号传播距离和速度的函数

传输延迟：把消息中的所有比特转移到链路中需要的时间，是消息长度和链路速率的函数

处理延迟：处理分组首部，检查位错误及确定分组目标

排队延迟：到来的分组排队等待处理的时间

以上加起来的时间就是客户端到服务器的总延迟时间。传播时间取决于距离和信号通过的媒介。另外传播速度一般不超过光速，而传输时延是由传输链路上的速率来决定的，与客户端和服务器的距离无关。假设有一个 10 MB 的文件，分别通过两个链路传输，一个1Mbit/s，另一个 100 Mbit/s。在 1 Mbit/s 的链路上，需要花10 s，而在 100 Mbit/s 的链路上，只需 0.1 s。

接着，分组到达路由器。路由器必须检测分组的首部，以确定出站路由，并且还可能对数据进行检查，这些都要花时间。由于这些检查通常由硬件完成，因此相应的延迟一般非常短，但再短也还是存在。最后，如果分组到达的速度超过了路由器的处理能力，那么分组就要在入站缓冲区排队。数据在缓冲区排队等待的时间，当然就是排队延迟。

每个分组在通过网络时都会遇到这样或那样的延迟。发送端与接收端的距离越远，传播时间就越长。一路上经过的路由器越多，每个分组的处理和传输延迟就越多。最后，网络流量越拥挤，分组在入站缓冲区中被延迟的可能性就越大。

## 浏览器网络概述

现代浏览器完全包括数百个组件的操作系统，包括进程管理，安全沙箱，分层的优化缓存，javascript虚拟机，图形渲染，GPU管道，存储系统，传感器，音频与视频，网络机制，等等。

显然，浏览器乃至运行在其中的应用的性能，取决于若干组件：解析、布局、HTML 与 CSS 的样式计算、JavaScript 执行速度、渲染管道，当然还有网络相关各层协议的配合。其中每个组件的角色都很重要，而网络组件通常是加倍重要，因为浏览器慢就慢在等待网络资源上，等待造成后续环节被阻塞！

运行在浏览器中的 Web 应用并不负责管理个别网络套接字的生命周期，这是好事。通过把这个任务委托给浏览器，可以自动化很多重要的性能优化任务，包括套接字重用、请求优先级排定、晚绑定、协议协商、施加连接数限制，等等。事实上，浏览器是有意把请求管理生命周期与套接字管理分开的

套接字是以池的形式进行管理的（图 14-2），即按照来源，每个池都有自己的连接限制和安全约束。挂起的请求是排好队的、有优先次序的，然后再适时把它们绑定到池中个别的套接字上。除非服务器有意关闭连接，否则同一个套接字可以自动用于多个请求

**来源**

由应用协议、域名和端口三个要件构成，比如 (http, www.example.com, 80) 与(https, www.example.com, 443) 就是两个不同的来源。

**套接字池**

属于同一个来源的一组套接字。实践中，所有主流浏览器的最大池规模都是 6 个套接字。

自动化的套接字池管理会自动重用 TCP 连接，从而有效保障性能，除此之外，这种架构设计还提供了其他优化的机会：

- 浏览器可以按照优先次序发送排队的请求；
- 浏览器可以重用套接字以最小化延迟并提升吞吐量；
- 浏览器可以预测请求提前打开套接字；
- 浏览器可以优化何时关闭空闲套接字；
- 浏览器可以优化分配给所有套接字的带宽

### 网络安全与沙箱

将个别套接字的管理任务委托给浏览器还有另一个重要的用意：可以让浏览器运用沙箱机制，对不受信任的应用代码采取一致的安全与策略限制。比如，浏览器不允许直接访问原始网络套接字 API，因为这样给恶意应用向任意主机发起任意请求（端口扫描、连接邮件服务器或发送未知消息）提供可乘之机

**连接限制**

浏览器管理所有打开的套接字池并强制施加连接数限制，保护客户端和服务器的资源不会被耗尽

**请求格式化与响应处理**

浏览器格式化所有外发请求以保证格式一致和符合协议的语义，从而保护服务器。类似地，响应解码也会自动完成，以保护用户。

**TLS协商**

浏览器执行 TLS 握手和必要的证书检查。任何证书有问题（比如服务器正在使用自已签发的证书），用户都会收到通知。

**同源策略**

浏览器会限制应用只能向哪个来

### 资源与客户端状态缓存

最好最快的请求是没有请求。在分派请求之前，浏览器会自动检查其资源缓存，执行必要的验证，然后在满足限制条件的情况下返回资源的本地副本。类似地，如果某本地资源不在缓存中，那么浏览器就会发送网络请求，将响应自动填充到缓存中，以备后续访问使用。

• 浏览器针对每个资源自动执行缓存指令。

• 浏览器会尽可能恢复失效资源的有效性。

• 浏览器会自动管理缓存大小及资源回收。

高效、最优地管理缓存很困难。所幸，浏览器会替我们照管这一切，我们要做的，只是确保服务器返回适当的缓存指令Cache-Control、ETag 和 Last-Modified。最后，浏览器还有一个经常被人忽视的重要功能，那就是提供会话认证和 cookie 管理。浏览器为每个来源维护着独立的 cookie 容器，为读写新 cookie、会话和认证数据提供必要的应用及服务器 API，还会为我们自动追加和处理 HTTP 首部，让一切都自动化。

### 应用API与协议

|                | XmlHttpRequest | Server-Sent Event | WebSocket  |
| -------------- | -------------- | ----------------- | ---------- |
| 请求流         | 否             | 否                | 是         |
| 响应流         | 受限           | 否                | 是         |
| 分帧机制       | HTTP           | 事件流            | 二进制分帧 |
| 二进制数据传输 | 是             | 否(base64)        | 是         |
| 压缩           | 是             | 是                | 受限       |
| 应用传输协议   | HTTP           | TCP               | WebSocket  |
| 网络传输协议   | TCP            | TCP               | TCP        |

### XMLHttpRequest

这就意味着浏览器会自动帮

我们完成所有底层的连接管理、协议协商、HTTP 请求格式化，以及更多工作：XHR不仅实现了浏览器的异步通信，还极大的简化的这个过程，XH R是浏览器提供的应用API.这就意味着浏览器会自动帮我们完成所有底层的连接管理、协议协商、HTTP 请求格式化，以及更多工作：

浏览器管理着连接建立、套接字池和连接终止；

浏览器决定最佳的 HTTP（S）传输协议（HTTP 1.0、1.x 和 2.0）； 

浏览器处理 HTTP 缓存、重定向和内容类型协商；

浏览器保障安全、验证和隐私；

等等

#### 跨源资源共享（CORS）

XHR 是一个浏览器层面的 API，向我们隐藏了大量底层处理，包括缓存、重定向、内容协商、认证，等等。这样做有两个目的。第一，XHR 的 API 因此非常简单，开发人员可以专注业务逻辑。其次，浏览器可以采用沙箱机制，对应用代码强制施加一套安全限制。

XHR 接口强制要求每个请求都严格具备 HTTP 语义：应用提供数据和 URL，浏览器格式化请求并管理每个连接的完整生命周期。类似地，虽然 XHR API 允许应用添加自定义的 HTTP 首部（通过 setRequestHeader() 方法），同时也有一些首部是应用代码不能设定的：

• Accept-Charset、Accept-Encoding、Access-Control-*

• Host、Upgrade、Connection、Referer、Origin

• Cookie、Sec-*、Proxy-* 以及很多其他首部

浏览器会拒绝对不安全首部的重写，以此保证应用不能假扮用户代理、用户或请求来源。事实上，保护来源（Origin）首部特别重要，因为这是对所有 XHR 请求应用“同源策略”的关键。

CORS 请求也使用相同的 XHR API，区别仅在于请求资源用的 URL 与当前脚本并不同源。

针对 CORS 请求的选择同意认证机制由底层处理：请求发出后，浏览器自动追加受保护的 Origin HTTP 首部，包含着发出请求的来源。相应地，远程服务器可以检查 Origin首部，决定是否接受该请求，如果接受就返回 Access-Control-Allow-Origin 响应首部：



```
=> 请求

GET /resource.js HTTP/1.1

Host: thirdparty.com

Origin: http://example.com ➊//Origin 首部由浏览器自动设置

...

<= 响应

HTTP/1.1 200 OK

Access-Control-Allow-Origin: http://example.com ➋// 选择同意首部由服务器设置
```

假如它选择不同意接受这个请求，那么只要不在响应中包含 *Access-Control-Allow-Origin* 首部即可。这样，客户端的浏览器就会自动将发出的请求作废。

如果第三方服务器不支持 CORS，那么客户端请求同样会作废，因为客户端会验证响应中是否包含选择同意的首部。作为一个特例，CORS 还允许服务器返回一个通配值 (Access-Control-Allow-Origin: *)，表示它允许来自任何源的请求。

**因为 CORS 还会提前采取一系列安全措施，以确保服务器支持 CORS：** 

• CORS 请求会省略 cookie 和 HTTP 认证等用户凭据；

• 客户端被限制只能发送“简单的跨源请求”，包括只能使用特定的方法（GET、POST 和 HEAD），以及只能访问可以通过 XHR 发送并读取的 HTTP 首部。

要启用 cookie 和 HTTP 认证，客户端必须在发送请求时通过 XHR 对象发送额外的属性（withCredentials），而服务器也必须以适当的首部（*Access-Control-Allow**Credentials*）响应，表示它允许应用发送用户的隐私数据。

```
=> 预备请求
OPTIONS /resource.js HTTP/1.1 ➊//验证许可的预备 OPTIONS 请求
Host: thirdparty.com
Origin: http://example.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: My-Custom-Header
...
<= 预备响应
HTTP/1.1 200 OK ➋//第三方源的成功预备响应
Access-Control-Allow-Origin: http://example.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: My-Custom-Header
...
（正式的 HTTP 请求）➌//实际的 CORS 请求
```

W3C 官方的 CORS 规范规定了何时何地必须使用预备请求：“简单的”请求可以跳过它，但很多条件下这个请求都是必需的，因此也会为验证许可而增加仅有一次往返的网络延迟。只要完成预备请求，客户端就会将结果缓存起来，后续请求就不必重复验证了

**<u>CORS 得到了所有现代浏览器支持，参见：caniuse.com/cors。要全面了解CORS 的各种策略及实现，请参考 W3C 官方标准（http://www.w3.org/TR/cors/）。</u>**

#### 通过XHR下载数据

XHR 既可以传输文本数据，也可以传输二进制数据。事实上，浏览器可以自动为各种原生数据类型提供编码和解码服务，因此应用在直接将这些数据传给 XHR 时就已经编码 / 解码好了，反之亦然。浏览器可以自动解码的数据类型如下。

ArrayBuffer

固定长度的二进制数据缓冲区。

Blob

二进制大对象或不可变数据。

Document

解析后得到的 HTML 或 XML 文档。

JSON

表示简单数据结构的 JavaScript 对象。

Text

简单的文本字符串。

浏览器通过http的content-type信息，来推断出类型， 比 如 把application/json 响应解析为 JSON 对象），应用也可以在发起 XHR 请求时显式重写数据类型：

```js
var xhr = new XMLHttpRequest();
xhr.open('GET', '/images/photo.webp');
xhr.responseType = 'blob'; ➊//将返回数据类型设置为 Blob
xhr.onload = function() {
 if (this.status == 200) {
 var img = document.createElement('img');
 img.src = window.URL.createObjectURL(this.response); ➋//基于返回的对象创建唯一的对象 URI 并设置为图片的源
 img.onload = function() {
 window.URL.revokeObjectURL(this.src); ➌//图片加载完毕后立即释放对象
 }
 document.body.appendChild(img);
 }
};
xhr.send()
```

注意，这里我们在以原生格式传输一张图片，没有使用 base64 编码，也没有使用数据 URI，而是在页面中添加了一个 <img> 元素。这样在 JavaScript 中处理接收到的二进制数据不会产生任何网络传输开销和编码开销！ XHR API 让我们得以通过脚本高效、动态地开发应用，无论操作什么数据类型都没问题，全部用 JavaScript搞定！

***这里的二进制大对象接口（Blob）属于 HTML5 的 File API，就像一个不透明的引用，可以指向任何数据块（二进制或文本）。这个对象本身没有太多功能，只能查询其大小、MIME 类型，或将它切分成更小的块。这个对象存在的真正目的，是作为各种 JavaScript API 之间的一种高效的互操作机制。***

#### 通过XHR上传数据

通过 XHR 上传任何类型的数据都很简单，而且高效。事实上，上传不同类型数据的代码都一样，只不过最后在调用 XHR 请求对象的 send() 方法时，要传入相应的数据对象。剩下的事就都由浏览器处理了：

```js
var xhr = new XMLHttpRequest();
xhr.open('POST','/upload');
xhr.onload = function() { ... };
xhr.send("text string"); ➊//把简单的文本字符串上传到服务器
var formData = new FormData(); ➋//通过 FormData API 动态创建表单数据
formData.append('id', 123456);
formData.append('topic', 'performance');
var xhr = new XMLHttpRequest();
xhr.open('POST', '/upload');
xhr.onload = function() { ... };
xhr.send(formData); ➌//向服务器上传 multipart/form-data 对象
var xhr = new XMLHttpRequest();
xhr.open('POST', '/upload');
xhr.onload = function() { ... };
var uInt8Array = new Uint8Array([1, 2, 3]); ➍//创建无符号、8 字节整型的有类型数组（ArrayBuffer）
xhr.send(uInt8Array.buffer); ➎//向服务器上传字节块
```

XHR 对象的 send() 方法可以接受 DOMString、Document、FormData、Blob、File 及ArrayBuffer 对象，并自动完成相应的编码，设置适当的 HTTP 内容类型 (content-type)，然后再分派请求。需要发送二进制 Blob 或上传用户提交的文件？简单，取得对该对象的引用，传给 XHR

如果上传文件过大，还可以将文件分块上传

```js
var blob = ...; ➊//任意数据（二进制或文本）的二进制对象
const BYTES_PER_CHUNK = 1024 * 1024; ➋//将块大小设置为 1 MB
const SIZE = blob.size;
var start = 0;
var end = BYTES_PER_CHUNK;
while(start < SIZE) { ➌//以 1 MB 为步长迭代数据块
 var xhr = new XMLHttpRequest();
 xhr.open('POST', '/upload');
 xhr.onload = function() { ... };
 xhr.setRequestHeader('Content-Range', start+'-'+end+'/'+SIZE); ➍//告诉服务器上传的数据范围（开始位置 - 结束位置 / 总大小）
 xhr.send(blob.slice(start, end)); ➎//通过 XHR 上传 1 MB 大小的数据片段
 start = end;
 end = start + BYTES_PER_CHUNK;
}
```

XHR 不支持请求流，这意味着在调用 send() 时必须提供完整的文件。不过，前面的例子示范了一个简单的解决方案：切分文件，然后通过多个 XHR 请求分段上传。这种实现方案当然不能替代真正的请求流 API，但对某些应用来说却是一个可行的方案。

***切分大文件上传是个不错的技巧，适合连接不稳定或经常中断的场景。此时，假如某个块由于掉线而上传失败，应用可以随后只重新上传该块，而不必重新上传整个大文件。***

#### 监控下载和上传进度

因为网络连接可能中断，而延时或者带宽也是高度不稳定。XHR提供了监听进度的事件API

| 事件类型  | 说明       | 触发次数 |
| --------- | ---------- | -------- |
| loadstart | 传输已开始 | 一次     |
| progress  | 正在传输   | 零或一次 |
| error     | 传输出错   | 零或一次 |
| abort     | 传输中止   | 零或一次 |
| load      | 传输成功   | 零或一次 |
| loadend   | 传输完成   | 一次·    |

每个 XHR 请求开始时都会触发 loadstart 事件，而结束时都会触发 loadend 事件。在这两事件之间，还可能触发一或多个其他事件，表示传输状态。因此，要监控进度，可以在 XHR 对象上注册一系列 JavaScript 事件监听器：

```js
var xhr = new XMLHttpRequest();
xhr.open('GET','/resource');
xhr.timeout = 5000; //➊设置请求的超时时间为 5000 ms（默认无超时限制）
xhr.addEventListener('load', function() { ... }); //➋为请求成功注册回调
xhr.addEventListener('error', function() { ... }); //➌为请求失败注册回调
var onProgressHandler = function(event) { if(event.lengthComputable) {
 var progress = (event.loaded / event.total) * 100; //➍计算传输进度
 ...
 }
}
xhr.upload.addEventListener('progress', onProgressHandler);// ➎为上传进度事件注册回调
xhr.addEventListener('progress', onProgressHandler);// ➏为下载进度事件注册回调
xhr.send();
```

无 论 load 和 error 中 的 哪 一 个 被 触 发 了， 都 代 表 XHR 传 输 的 最 终 状 态， 而progress 事件则可能触发任意多次，这就为监控传输状态提供了便利：我们可以比较 loaded 与 total 属性，估算传输完成的数据比例。

**<u>*要估算传输完成的数据量，服务器必须在其响应中提供内容长度（ContentLength）首部。而对于分块数据，由于响应的总长度未知，因此就无法估计进度了。*</u>**

**<u>*另外，XHR 请求默认没有超时限制，这意味着一个请求的“进度”可以无限长。作为最佳实践，一定要为应用设置合理的超时时间，并适当处理错误。</u>***

#### 通过XHR实现流式数据传输

XHR很难实现流传输。

**上传时，send 方法只接受完整的载荷**

**response、responseText 和 responseXML 属性也不是为流设计的**

虽然没有官方的api为实现流的传输的，但是还有有人提供了一些方法来实现：

```
Web 应用必须有能力获得并操作各种形式的数据，包括随着时间推移逐渐可用的一系列数据。本规范定义了流的基本表示法、流触发的错误，以及通过
编程方式读取和创建流的方式。
																																															——W3C Streams API
```

但是这种xhr+streams的方式还没有浏览器实现，但是虽然通过上传无法实现这种方式，但是下载还是可以通过流传输的

```js
var xhr = new XMLHttpRequest();
xhr.open('GET', '/stream');
xhr.seenBytes = 0;
xhr.onreadystatechange = function() { ➊//预订状态和进度通知
 if(xhr.readyState > 2) {
 var newData = xhr.responseText.substr(xhr.seenBytes); ➋//从部分响应中提取新数据
 // 处理 newData
 xhr.seenBytes = xhr.responseText.length; ➌//更新处理的字节偏移量
 }
};
xhr.send();
```

这个例子在多数现代浏览器中都可以运行，但性能并不理想。另外，还有很多关于实现的问题和注意事项。

我们是手工跟踪看到的字节的偏移量，然后再手工切分数据：responseText 则缓冲了完整的响应！对于少量数据传输而言，这不是问题。但如果下载的数据很大，特别是在内存十分有限的设备比如手机上，这就是问题了。释放缓冲数据的唯一方式就是完成当前请求，并且打开一个新请求。部分响应只能通过读取 responseText 属性获取，因此也就只能局限于文本数据了。没有办法部分读取二进制数据的响应。读取完部分数据后，我们必须自己标识数据的界限：应用代码必须定义自己的数据格式，然后再缓冲并解析数据流，提取出相应的信息。浏览器缓冲收到数据的方式也不相同：有的会立即释放，而有的只缓冲小响应，对较大的响应块则立即释放。浏览器允许递增读取的数据类型也不一样：有的允许 text/html，而有的只允许application/x-javascript。

虽然 XHR 满足不了我们的要求，我们还有其他办法，而且是专门为流式数据处理设计的：Server-Sent Events 提供方便的流 API，用于从服务器向客户端发送文本数据，而 WebSocket 则提供了高效、双向的流机制，而且同时支持二进制和文本数据。

#### 实时通知与交付

XHR 提供了一种简单有效的客户端与服务器同步的方式：必要时，客户端可以向服务器发送一个 XHR 请求，以更新服务器上的相应数据。然而，实现同样但相反的操作却要困难一些。如果服务器的数据更新了，那怎么通知客户端呢？主流浏览器对 XHR 流的支持有限，那我们就只能使用 XHR 轮询了

##### 通过XHR实现轮询

从服务器取得更新的一个最简单的办法，就是客户端在后台定时发起 XHR 请求，也就是轮询（polling）。如果服务器有新数据，返回新数据，否则返回空响应。

轮询实现起来简单，但也经常效率很低。其中关键在于选择轮询间隔：长轮询间隔意味着延迟交付，而短轮询间隔会导致客户端与服务器间不必要的流量和协议开销

```
function checkUpdates(url) {
 var xhr = new XMLHttpRequest();
 xhr.open('GET', url);
 xhr.onload = function() { ... }; ➊
 xhr.send();
}
setInterval("checkUpdates('/updates'), 60000"); ➋
```

##### 通过XHR实现长轮询

定时轮询的一个大问题就是很可能造成大量没必要的空检查。我们需要对此进行改进，在没有更新的时候不再返回空响应，而是把连接保持到有更新的时候。

通过将连接一直保持打开到有更新（长轮询），就可以把更新立即从服务器发送给客户端。这样，长轮询就解决了消息交付延迟的问题，同时也消灭了空检查，减少了XHR 请求次数和轮询的整体开销。在交付更新后，长轮询请求完成，然后客户端再发送下一次长轮询请求，等待下一次更新：

```js
function checkUpdates(url) {
 var xhr = new XMLHttpRequest();
 xhr.open('GET', url);
 xhr.onload = function() { ➊//处理更新并打开新的长轮询 XHR
 ...
 checkUpdates('/updates'); ➋//发送长轮询请求并等待下次更新（如此不停循环）
 };
 xhr.send();
}
checkUpdates('/updates'); ➌//发送第一次长轮询 XHR 请求
```

这样的话，是不是可以说长轮询永远都比定时轮询好呢？除非更新到达频率已知且固定，否则长轮询的延迟总是最短的。如果延迟是一个重要考虑因素，那么长轮询就是最好方案

从另一方面看，还需要更仔细地分析一下开销。首先，每次更新都会伴有相同的HTTP 开销，即每次更新都需要一次独立的 HTTP 请求。如果更新频率很高，那么长轮询又会导致比定时轮询更多的 XHR 请求！

长轮询通过最小化延迟可以动态适应更新频率，这种行为可能是也可能不是我们所期望的。如果应用可以容许一定时间的延迟，那么定时轮询可能更有效。因为在更新频率很高的情况下，定时轮询就是一个简单的“更新累积”机制，不仅能减少请求次数，还能减少对手机电量的消耗。

### 服务器发送事件

Server-Sent Events（SSE）让服务器可以向客户端流式发送文本消息，比如服务器上生成的实时通知或更新。为达到这个目标，SSE 设计了两个组件：浏览器中的EventSource 和新的“事件流”数据格式。其中，EventSource 可以让客户端以 DOM事件的形式接收到服务器推送的通知，而新数据格式则用于交付每一次更新

EventSource API 和定义完善的事件流数据格式，使得 SSE 成为了在浏览器中处理实时数据的高效而不可或缺的工具：

通过一个长连接低延迟交付；

高效的浏览器消息解析，不会出现无限缓冲；

自动跟踪最后看到的消息及自动重新连接；

消息通知在客户端以 DOM 事件形式呈现。

实际上，SSE 提供的是一个高效、跨浏览器的 XHR 流实现，消息交付只使用一个长 HTTP 连接。然而，与我们自己实现 XHR 流不同，浏览器会帮我们管理连接、解析消息，从而让我们只关注业务逻辑。

#### EventSource API

EventSource 接口通过一个简单的浏览器 API 隐藏了所有的底层细节，包括建立连接和解析消息。要使用它，只需指定 SSE 事件流资源的 URL，并在该对象上注册相应 JavaScript 事件监听器即可：

```js
var source = new EventSource("/path/to/stream-url"); //➊打开到流终点的 SSE 连接
source.onopen = function () { ... }; //➋可选的回调，建立连接时调用
source.onerror = function () { ... };//➌可选的回调，连接失败时调用
source.addEventListener("foo", function (event) { //➍监听 "foo" 事件，调用自定义代码
 processFoo(event.data);
});
source.onmessage = function (event) { //➎监听所有事件，不明确指定事件类型
 log_message(event.id, event.data);
 if (event.id== "CLOSE") {
 source.close(); //➏如果服务器发送 "CLOSE" 消息 ID，关闭 SSE 连接
 }
}
```

***EventSource 可以像常规 XHR 一样利用 CORS 许可及选择同意机制，实现客户端到远程服务器的流式事件数据传输。***

***SSE 实现了节省内存的 XHR 流。与原始的 XHR 流在连接关闭前会缓冲接收到的所有响应不同，SSE 连接会丢弃已经处理过的消息，而不会在内存中累积***

EventSource 接口还能自动重新连接并跟踪最近接收的消息：如果连接断开了，EventSource 会自动重新连接到服务器，还可以向服务器发送上一次接收到的消息 ID，以便服务器重传丢失的消息并恢复流。

针对不支持SSE的浏览器我们可以这么处理：

```js
if (!window.EventSource) {
 // 加载 JavaScript 腻子脚本
}
var source = new EventSource("/event-stream-endpoint");
```

使用腻子脚本的好处，仍然是让我们只关注应用逻辑，而不是因浏览器支持情况闹心。

不支持的可以使用XHR的轮询。腻子脚本只是提供了一致的 API，底层的 XHR 传输机制依旧不那么高效：

XHR 轮询会导致消息延迟和很高的请求开销；

XHR 长轮询能最小化延迟，但开销还是很高；

XHR 对流的支持有限，且在内存中缓冲所有数据

#### Event Stream协议

SSE 事件流是以流式 HTTP 响应形式交付的：客户端发起常规 HTTP 请求，服务器以自定义的“text/event-stream”内容类型响应，然后交付 UTF-8 编码的事件数据。

```js
=> 请求
GET /stream HTTP/1.1 //➊ 客户端通过 EventSource 接口发起连接
Host: example.com
Accept: text/event-stream
<= 响应
HTTP/1.1 200 OK //➋ 服务器以 "text/event-stream" 内容类型响应
Connection: keep-alive
Content-Type: text/event-stream
Transfer-Encoding: chunked
retry: 15000 //➌ 服务器设置连接中断后重新连接的间隔时间（15 s）
data: First message is a simple string. //➍ 不带消息类型的简单文本事件
data: {"message": "JSON payload"} //➎ 不带消息类型的 JSON 数据载荷
event: foo //➏ 类型为 "foo" 的简单文本事件
data: Message of type "foo"
id: 42 // ➐带消息 ID 和类型的多行事件
event: bar
data: Multi-line message of
data: type "bar" and id "42"
id: 43 //➑带可选 ID 的简单文本事件
data: Last message, id "43"
```

以上事件流协议很好理解，也很好实现：

• 事件载荷就是一或多个相邻 data 字段的值；

• 事件可以带 ID 和 event 表示事件类型；

• 事件边界用换行符标识。

在接收端，EventSource 接口通过检查换行分隔符来解析到来的数据流，从 data 字段中提取有效载荷，检查可选的 ID 和类型，最后再分派一个 DOM 事件告知应用。如果存在某个类型，那么就会触发自定义的 DOM 事件处理程序；否则，就会调用通用的 onmessage 回调

##### SSE 中的 UTF-8 编码与二进制传输

EventSource 不会对实际载荷进行任何额外处理：从一或多个 data 字段中提取出来的消息，会被拼接起来直接交给应用。因此，服务器可以推送任何文本格式（例如，简单字符串、JSON，等等），应用必须自己解码。话虽如此，但所有事件源数据都是 UTF-8 编码的：SSE 不是为传输二进制载荷而设计的！如果有必要，可以把二进制对象编码为 base64 形式，然后再使用 SSE。但这样会导致很高（33%）的字节开销，担心 UTF-8 编码也会造成高开销？ SSE 连接本质上是 HTTP 流式响应，因此响应是可以压缩的（如 gzip 压缩），就跟压缩其他 HTTP 响应一样，而且是动态压缩！虽然 SSE 不是为传输二进制数据而设计的，但它却是一个高效的机制——只要让你的服务器对 SSE 流应用 gzip 压缩。不支持二进制传输是有意为之的。SSE 的设计目标是简单、高效，作为一种服务器向客户端传送文本数据的机制。如果你想传输二进制数据，WebSocket 才是更合适的选择

最后，除了自动解析事件数据，SSE 还内置支持断线重连，以及恢复客户端因断线而丢失的消息。默认情况下，如果连接中断，浏览器会自动重新连接。SSE 规范建议的间隔时间是 2~3 s，这也是大多数浏览器采用的默认值。不过，服务器也可以设置一个自定义的间隔时间，只要在推送任何消息时向客户端发送一个 retry 命令即可。

类似地，服务器还可以给每条消息关联任意 ID 字符串。浏览器会自动记录最后一次收到的消息 ID，并在发送重连请求时自动在 HTTP 首部追加“Last-Event-ID”值。下面看一个例子：

```js
（既有 SSE 连接）
retry: 4500 //➊ 服务器将客户端的重连间隔设置为 4.5 s
id: 43 //➋ 简单文本事件，ID:43
data: Lorem ipsum
（连接断开）
（4500 ms 后）
=> 请求
GET /stream HTTP/1.1 //➌ 带最后一次事件 ID 的客户端重连请求
Host: example.com
Accept: text/event-stream
Last-Event-ID: 43
<= 响应
HTTP/1.1 200 OK //➍ 服务器以 'text/event-stream' 内容类型响应
Content-Type: text/event-stream
Connection: keep-alive
Transfer-Encoding: chunked
id: 44 //➎ 简单文本事件，ID:44
data: dolor sit amet
```

客户端应用不必为重新连接和记录上一次事件 ID 编写任何代码。这些都由浏览器自动完成，然后就是服务器负责恢复了。值得注意的是，根据应用的要求和数据流，服务器可以采取不同的实现策略。

如果丢失消息可以接受，就不需要事件 ID 或特殊逻辑，只要让客户端重连并恢复数据流即可。如果必须恢复消息，那服务器就需要指定相关事件的 ID，以便客户端在重连时报告最后接收到的 ID。同样，服务器也需要实现某种形式的本地缓存，以便恢复并向客户端重传错过的消息。

当然，像要保留多少条消息这种细节一定取决于具体的应用。另外，要知道 ID 是可选的事件流字段。而服务器也可以在交付的事件流中对特定消息设置检查点或者里程碑标记。一句话，根据你的需求，实现服务器逻辑。

#### SSE使用场景及性能

SSE 是服务器向客户端发送实时文本消息的高性能机制：服务器可以在消息刚刚生成就将其推送到客户端（低延迟），使用长连接的事件流协议，而且可以 gzip 压缩（低开销），浏览器负责解析消息，也没有无限缓冲。再加上超级简单的 EventSource API 能自动重新连接和把消息通知作为 DOM 事件，使得 SSE 成为处理实时数据不可或缺的得力工具！

SSE 主要有两个局限。一，只能从服务器向客户端发送数据，不能满足需要请求流的场景（比如向服务器流式上传大文件）；二，事件流协议设计为只能传输 UTF-8数据，即使可以传输二进制流，效率也不高。话虽如此，UTF-8 的限制往往可以在应用层克服：SSE 可以通知应用说服务器上有一个新的二进制文件可以下载了，应用只要再分派一个 XHR 请求去下载即可。虽然这样多了一次往返延迟，但也能利用上 XHR 提供的诸多便利：响应缓存、传输编码（压缩），等等。如果文件是流式下载的，那它就无法被浏览器缓存。

实时推送就像轮询一样，可能会极大影响电池的待机时间。首先，可以考虑批量处理消息，尽量少唤醒无线电模块。其次，避免不必要的长连接，SSE 连接在无线电空闲时不会断开。更多信息，请参考 “**消除周期性及无效的数据传输**”。

```
                                          通过 TLS 实现 SSE 流
 SSE 通过常规 HTTP 连接实现了简单便捷的实时传输机制，服务器端容易部署，客户端也容易打补丁。可是，现有网络中间设备，比如代理服务器和防火墙，都不支持 SSE，而这有可能带来问题：中间设备可能会缓冲事件流数据，导致额外延迟，甚至彻底毁掉 SSE 连接。如果你碰到了这样或类似的问题，那么可以考虑通过 TLS 发送 SSE 事件流，具体请参考 4.1 节中的“Web 代理、中间设备、TLS 与新协议”。
```

### WebSocket

WebSocket 可以实现客户端与服务器间双向、基于消息的文本或二进制数据传输。它是浏览器中最靠近套接字的 API。但 WebSocket 连接远远不是一个网络套接字，因为浏览器在这个简单的 API 之后隐藏了所有的复杂性，而且还提供了更多服务：

连接协商和同源策略；

与既有 HTTP 基础设施的互操作；

基于消息的通信和高效消息分帧；

子协议协商及可扩展能力。

WebSocket 是浏览器中最通用最灵活的一个传输机制，其极简的 API 可以让我们在客户端和服务器之间以数据流的形式实现各种应用数据交换（包括 JSON 及自定义的二进制消息格式），而且两端都可以随时向另一端发送数据。

不过，自定义数据交换协议的问题通常也在于自定义。因为应用必须考虑状态管理、压缩、缓存及其他原来由浏览器提供的服务。设计限制和性能权衡始终会有，利用WebSocket 也不例外。简单来说，WebSocket 并不能取代 HTTP、XHR 或 SSE，而为了追求最佳性能，关键还是要利用这些机制的长处。

WebSocket 由多个标准构成：WebSocket API 是 W3C 定义的，而 WebSocket协议（RFC 6455）及其扩展则由 HyBi Working Group（IETF）定义

#### WebSocket API

```js
var ws = new WebSocket('wss://example.com/socket'); //➊ 打开新的安全 WebSocket 连接（wss）
ws.onerror = function (error) { ... } //➋ 可选的回调，在连接出错时调用
ws.onclose = function () { ... } //➌ 可选的回调，在连接终止时调用
ws.onopen = function () { //➍ 可选的回调，在 WebSocket 连接建立时调用
 ws.send("Connection established. Hello server!"); //➎ 客户端先向服务器发送一条消息
}
ws.onmessage = function(msg) { //➏回调函数，服务器每发回一条消息就调用一次
 if(msg.data instanceof Blob) { //➐ 根据接收到的消息，决定调用二进制还是文本处理逻辑
 processBlob(msg.data);
 } else {
 processText(msg.data);
 }
}
```

##### WS与WSS

WebSocket 资源 URL 采用了自定义模式：ws 表示纯文本通信（如 ws://example.com/socket），wss 表示使用加密信道通信（TCP+TLS）。WebSocket 的主要目的，是在浏览器中的应用与服务器之间提供优化的、双向通信机制。可是，WebSocket 的连接协议也可以用于浏览器之外的场景，景，可以通过非 HTTP协商机制交换数据。考虑到这一点，HyBi Working Group 就选择采用了自定义的

URL 模式。

**使用自定义的 URL 模式虽然让非 HTTP 协商成为可能，但实践中还没有既定标准可以作为建立 WebSocket 会话的替代握手机制。**

##### 接收文本和二进制数据

WebSocket 通信只涉及消息，应用代码无需担心缓冲、解析、重建接收到的数据。比如，服务器发来了一个 1 MB 的净荷，应用的 onmessage 回调只会在客户端接收到全部数据时才会被调用

此外，WebSocket 协议不作格式假设，对应用的净荷也没有限制：文本或者二进制数据都没问题。从内部看，协议只关注消息的两个信息：净荷长度和类型（前者是一个可变长度字段），据以区别 UTF-8 数据和二进制数据。

浏览器接收到新消息后，如果是文本数据，会自动将其转换成 DOMString 对象，如果是二进制数据或 Blob 对象，会直接将其转交给应用。唯一可以（作为性能暗示和优化措施）多余设置的，就是告诉浏览器把接收到的二进制数据转换成 ArrayBuffer而非 Blob：

```js
var ws = new WebSocket('wss://example.com/socket');
ws.binaryType = "arraybuffer"; //➊ 如果接收到二进制数据，将其强制转换成 ArrayBuffer
ws.onmessage = function(msg) {
 if(msg.data instanceof ArrayBuffer) {
 processArrayBuffer(msg.data);
 } else {
 processText(msg.data);
 }
}
```

------

用户代理可以将这个选项看作一个暗示，以决定如何处理接收到的二进制数

据：如果这里设置为“blob”，那就可以放心地将其转存到磁盘上；而如果

设置为“arraybuffer”，那很可能在内存里处理它更有效。自然地，我们鼓励

用户代理使用更细微的线索，以决定是否将到来的数据放到内存里……

——The WebSocket API 

W3C Candidate Recommendation 
