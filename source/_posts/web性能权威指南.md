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
