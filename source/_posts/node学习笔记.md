---
title: node学习笔记
date: 2022-09-09 07:58:19
tags:
- [Node]
categories:
- [Node,note]
---


```
1.node的输出
	console.log()
	console.clear()
	console.trace()
2.常见的全局对象
	特殊的全局对象，是各个模块都有的，但不是属于全局的
	__dirname//获取当前文件所在的路径，不包括后面的文件名
	__filename//获取当前文件所在的路径名称包括文件名
	exports
	module
	require
	
	常见的全局对象
		process对象
		console对象
		定时器对象
3.global和window的区别
	node中每一个文件都是一个独立的模块
4.模块化
	立即调用函数(IIFE)是解决模块化的一种方式
	CommonJS(CJS)
	ESmodule
	每个模块都有exports,他会默认指向空对象,exports指向的一个对象并在另外一个模块中被引用时，他们是指向同一个对象
	require是对exports的浅拷贝
	浅拷贝的本质就是引用赋值
	exports中修改变量会使require发生变化
	require中的修改不会对exports造成影响，因为require是对exports的拷贝
5.module.exports
	每一个模块都有一个module实例
	commomjs中没有module.export的概念，node中使用的是Moudle类，每一个模块都是Modlue的实例，也是module
	所以在Node中真正导出的其实不是export,而是module.exports
	为什么exports也可以导出
		因为module对象的exports属性是export对象的一个引用，也就是module.exports=exports
		module.export导出的值才是require的值，默认module.export==export,如果module.export指向另外一个值，则require会引用这个值，而不是export的值
	module.export的赋值是在顶层的
	
	module.exports=exports
	
	exports=123//module.exports在顶层引用的，如果后面再次修改exports的指向，则会无效
6.require()的查找规则
	1.如果x是核心模块，直接会返回核心模块，require("x")//会直接返回，停止查找
	2.x是以./或者../或者/开头的
		会先将x当作一个文件先查找，如果有后缀名，则按后缀名格式查找，如果没有后缀名则
			1.直接查找文件x
			2.查找x.js
			3.查找x.json
			4.查找x.node
		如果没有找到对应的文件，则会把他当成一个目录，查找里面的index文件
			1.x/index.js
			2.x/index.json
			3.x/index.node
		否则报错 not found
	3.如果直接是一个X(没有路径) 并且X不是一个核心模块
	则从最里面的node_module->最外面的node_module中去寻找
7.require的加载过程是同步的 
8.module.export={} exports=module.export===export={} module.export=exports
9.在源码中exports=module.exports
10.模块的加载过程
	模块在被第一次引入时，模块中的js代码会被运行一次
	模块被对此引入时，会被缓存，最终只会被加载一次
	如果有循环引入，他会按深度优先搜索遍历的方式进行加载
11.ES modules中
	export {}//不是一个对象，里面放置要到处的变量的列表
	import默认必须放到顶部，import是异步加载，import加载模块不可以放到逻辑代码中
	import().then()//进行动态加载
12.commonJS加载过程
	commonjs加载js文件的过程是运行时加载，并且是同步的，就是js引擎在执行代码的过程中加载模块
	同步意味着一个文件在没有加载结束之前，后面的代码都不加在
	commonjs通过module.exports的导出一个对象，导出的是一个对象意味者可以将这个对象的引用在其他模块中赋值给其他变量，但是最终他们会指向同一个变量，那么一个变量修改了对象的属性，所有地方都会修改
	注意：require进行解构的时候不会修改，因为解构会固定下当前的值
13.ES module的加载过程
   ES MODULE加载js是编译的时候加载的，并且是异步的
   编译时加载意味着import不能和运行相关的内容放在一起使用
   比如from后面的路径要动态获取
   比如不能将import放到if语句的代码块里面，所以es module时静态解析
   异步意味着js引擎遇到import时会获取到这个js文件，但是这个获取的过程是异步的，并不会阻塞进程的执行
   type-=module相当于script上的async属性，如果后面还有js代码，他不会阻塞js代码的执行
   ES Module通过export导出的是变量本身的引用：
	 export在导出一个变量时，js引擎会解析这个语法，并且创建模块环境记录（module environment 
record）；
 模块环境记录会和变量进行 绑定（binding），并且这个绑定是实时的；
 而在导入的地方，我们是可以实时的获取到绑定的最新值的；
  所以，如果在导出的模块中修改了变化，那么导入的地方可以实时获取最新的变量；
  注意：在导入的地方不可以修改变量，因为它只是被绑定到了这个变量上（其实是一个常量）
  
 14.在node中要使用es module
 	方式一：package.json中配置type:module
 	方式二:使用以.mjs结尾的文件
 15.内置模块path
 		从路径获取信息
 			dirname()//获取文件的父文件夹
 			basename()//获取文件名
 			extname()//获取文件扩展名
 		路径的拼接
 			如果我们希望将多个路径进行拼接，但是不同的操作系统可能是使用不同的分隔符
 			这个时候我们可以使用path.join函数
 		将文件和某个文件夹拼接
 			如果我们希望将某个文件和文件夹拼接，可以使用path.resolve
 			resolve函数会判断我们拼接的路径前面是否有/或者../或者./
 			如果有表示是一个绝对路径，会返回对应的拼接路径
 			如果没有，那么会和当前执行文件所在的文件夹进行路径的拼接
 			path.resolve() 方法将路径或路径片段的序列解析为绝对路径。
 			如果没有传入 path 片段，则 path.resolve() 将返回当前工作目录的绝对路径。
 			
 			path.resolve('/foo/bar', './baz');
            // 返回: '/foo/bar/baz'

            path.resolve('/foo/bar', '/tmp/file/');
            // 返回: '/tmp/file'

            path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
            // 如果当前工作目录是 /home/myself/node，
            // 则返回 '/home/myself/node/wwwroot/static_files/gif/image.gif'
 16.fs模块
 	fs操作文件的三种方式
```

![QQ截图20220618120700.png](https://img1.imgtp.com/2022/09/11/T30nJvRF.png)

17.文件描述符

​	在POSIX系统上，对于每个进程，内核都维护者一张当前打开的资源和文件的表格

​	每个打开的文件都会分配一个称为文件秒师傅的简单数字标识符

​	在系统层，所有系统操作都是使用这些文件描述符来标识和跟踪每一个特定文件

​	windows系统有不同的机制



​	fs.open()用于分配新的文件描述符，一旦被分配，则文件描述符可用于文件的读取数据，向文件写入数据或者请求关于文件的信息

![QQ截图20220619093740.png](https://img1.imgtp.com/2022/09/11/TXLJdkIn.png)

18.文件的读写

​	

```
fs.readFile(path,options?,callback)   //读取文件的内容
fs.writeFile(file,data,options?,callback) //写入文件
```

![QQ截图20220619094754.png](https://img1.imgtp.com/2022/09/11/gbD9UytN.png)

```
options中的属性
flag:写入的方式
	w:打开文件写入，默认值
	w+:打开文件进行读写，如果不存在则会创建文件
	r+:打开文件进行读写，如果不存在就会抛出异常
	r:打开文件读取，读取时的默认值
	a:打开要写入的文件，将流放在文件的末尾，如果不存在则会创建文件
	a+:打开文件进行读写，将流放到文件末尾，如果不存在则创建
encoding:字符的编码
	现在基本上使用utf-8
	如果不填encoding则会返回buffer
```

19.文件夹的操作

![QQ截图20220619093820.png](https://img1.imgtp.com/2022/09/11/ladw1Jt6.png)
20.events模块

​	 Node中的核心API都是基于异步事件驱动的：

​	 发出事件和监听事件都是通过EventEmitter类来完成，他们都是属于events对象

​	  

```
const emitter=new EventEmitter()
emitter.on(eventName.listener)//监听事件，也可以使用addListener
emitter.off(eventName.listener)//移出事件监听，也可以使用removeLitener
emitter.emit(eventName,...args?)//发出事件，可以携带参数
```

![QQ截图20220619135051.png](https://img1.imgtp.com/2022/09/11/gbfKncoB.png)
![QQ截图20220619135226.png](https://img1.imgtp.com/2022/09/11/z58jVfjS.png)
```shij
emitter.once(eventName,listener)事件只监听一次
emitter.prependListener()将监听事件添加到最前面
emitter.prependOnceListener()将监听事件添加到最前面但只监听一次
emitter.removeAllListeners(eventName?)移除所有事件
```

21、npm包管理工具

​			npm run start=>npm start

​			npm run test=>npm test

​			npm run stop=>npm stop

​			npm run restart=>npm restart

22.npm config get cache //cmd中执行查看缓存所在目录

23.Buffer类

​	使用Buffer来存储二进制

​	buf=Buffer.from()//转换成buffer

​	中文会转换成unicode编码  3个unicode编码代表一个汉字 默认是utf8编码

​	`buf.toString([encoding[, start[, end]]])`//解码 encode默认是utf8

​	buffer的另外一种创建方式

​	`Buffer.alloc(size[, fill[, encoding]])` size表示创建的大小 默认是utf8

​	buffer可以将图片转换成二进制流进行传输，也可以把二进制转换成图片存储

24.读取文件返回的是一个二进制流

![QQ截图20220623141058.png](https://img1.imgtp.com/2022/09/11/yyxGHUMi.png)
25.读取图片并且写入到另外一个文件

![QQ截图20220623141209.png](https://img1.imgtp.com/2022/09/11/TduZGuBK.png)
26.sharp插件进行设置图片

27.Buffer的创建过程

​	事实上我们创建Buffer时，并不会频繁的向操作系统申请内存，它会默认先申请一个8 * 1024个字节大小的内存， 也就是8kb

28.Buffer的源码

29.进程和线程

​	进程:启动一个程序，既可以默认开启一个进程（也可以多进程）

​	线程：操作系统能够运行调度的最小单位

30.Node事件循环的阶段

​	 一次完整的事件循环Tick分成很多个阶段

​	定时器(Timer):本阶段执行已经被 setTimeout() 和 setInterval() 的调度回调函数

​	待定回调（Pending Callback）：对某些系统操作（如TCP错误类型）执行回调，比如TCP连接时接收到 ECONNREFUSED

​	idle, prepare：仅系统内部使用

​	轮询（Poll）：检索新的 I/O 事件；执行与 I/O 相关的回调

​	检测：setImmediate() 回调函数在这里执行

​	关闭的回调函数：一些关闭的回调函数，如：socket.on('close', ...)

31.Node的微任务和宏任务

​	微任务队列

​	nettick queue:process.nextTick

​	other queue:Promise的then回调，queueMicrotask

​	宏任务队列

​	timer queue:setTimeout,setInterval	

​	poll queue :IO事件

​	check queue:setImmediate

​	close queue:close事件

32.Stream可以读取或者写入部分

​	4.中基本的流类型

​		writable:可以向其写人数据的流 fs.createWriteStream()

​		Readable:可以从中读取数据的流 fs.createReadStream()

​		Duplex：同时为Readable和wratable net.Socket

​		Transform:Duplex可以在写入和读取数据时修改或者转换数据的流  zlib.createDeflate()

33.Readable

![QQ截图20220624165628.png](https://img1.imgtp.com/2022/09/11/QggeUwVk.png)
34.writable

![QQ截图20220624183130.png](https://img1.imgtp.com/2022/09/11/14Ca9DGs.png)
35.pipe

从读取到输出到另外一个文件也可以这么写

![QQ截图20220624183130.png](https://img1.imgtp.com/2022/09/11/14Ca9DGs.png)
36.http

安装nodemon来热更新自动启动

37.axios可以在浏览器用也可以在node中用，浏览器用的是xhr,node中使用的是http内置模块

38.文件上传

39.express

​		express核心是中间件

​		安装express

​		1.创建一个文件夹 

​		2.npx express-generator

​		3.npm install

​		4.DEBUG=express:* npm start

​		第二种安装方式

```
npm init
npm install express

```

40.中间件

41.文件上传

​	使用multer  npm install multer

42.打印日志

​	使用morgan npm install morgan

43express路由

44.使用express部署静态资源，将express作为静态资源服务器

45.错误处理

46.调用express()到底创建的是什么

47.app.listen()启动服务器

​		如何可以结合原生来启动服务器

​		express->http.createServer.listen

48.app.use(中间件)内部发生了什么

49.用户发送了请求，中间件是如何被回调

50.next的为什么会执行下一个中间件

51.koa洋葱模型

​		koa的同步和异步都是符合洋葱模型的

​		express只有同步代码才会符合洋葱模型

52.koa和express的区别

​	
