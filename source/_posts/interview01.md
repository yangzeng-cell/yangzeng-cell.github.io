---
title: 牛客网收集面试题答案
date: 2022-08-04 11:46:19
categories:
- [interview, daily]
---

#### 1.说一说cookie sessionStorage localStorage 区别？

**我的回答：**cookie的大小只有4k,cookie是在浏览器中存储的，分为两种，内存cookie和硬盘cookie,默认的cookie就是内存cookie,没有设置过期时间，默认情况下就是浏览器关闭，cookie就会被浏览器删除，硬盘cookie是有设置过期时间，在过期时间之前，浏览器关闭，cookie不会被删除。把过期时间设置为负数或者0，可以删除cookie。

sessionStorage和localStorage不会自动存储，需要手动的存储数据，存储的数据量会比cookie大。sessionStorage只会在本次会话中存储，不会在新的页面中被获取。localStorage在页面关闭并且重新打开时不会被清除，sessionStorage会被清除。在页面内发生跳转不打开新的页面的时候，sessionStorage和localstorage不会被删除，在页面中跳转打开新的页面的时候，sessionStorage不保存，localstorage会保存

**参考答案：**得分点 数据存储位置，生命周期，存储大小，写入方式，数据共享，发送请求是否携带，应用场景。相同点Cookie,SessionStorage，LocalStorage都是存储在浏览器本地的，不同点，cookie默认是浏览器关闭时候会被删除，可以设置过期时间，在过期时间内浏览器关闭，cookie不会删除。cookie一般是由服务端写入的，SessionStorage和LocalStorage是在客户端写入的，cookie的生命周期是由服务端设置好的，而LocalStorage是永远存在的，如果没有手动删除。SessionStorage页面关闭会被清除。sessionStorage只会在本次会话中存储，不会在新的页面中被获取，页面内发生跳转不打开新的页面时候，sessionStorage不会被清除，cookie的存储大小为4k,sessionStorage和localStorage存储大小为5M。cookie，sessionStorage,localStorage数据共享都是遵循同源策略原则，SessionStorage还必须限制在同一个页面。前端向后端发送数据的时候，cookie会自动携带，而localStorage和sessionStorage中的数据必须手动添加。应用场景：cookie可以存储登录的验证信息SessionId和token,localStorage通常存储不易变动的数据来减轻服务器的压力，SessionStorage可以用来检测用户是否刷新进入页面，像音乐播放器恢复播放进度条的功能

#### 2.说一说JS数据类型有哪些,区别是什么？

数据类型有Null,undefined,Boolean,Number.String,Symbol,Bigint,Object8种数据类型

基本数据类型有null,undefined,boolean,number,string,symbol,bigint,引用数据类型包括普通的object，数组，正则，Date对象，Math内置对象，Function对象，包括ES6新增的Map和Set对象

基本数据类型是存储在栈中，引用数据类型存储在堆中的，引用数据类型在栈中存储了指针，该指针指向存储数据所在的堆内存的起始地址。

Symbol是ES6中新增的基本数据类型，可以产生一个独一无二的值，作为Object的key。Symbol创建的值具有唯一性，Symbol(key)!==Symbol(key),要获取对象中用symbol作为key的属性，必须通过Object.getOwnPropertySymbols(obj)

Bigint也是ES6新增的基本数据类型，来代表超出最大或者最小Number可以表示的值，不可以和Number混用

判断基本数据类型可以使用typeof 来判断，但是typeof null ===object typeof function==='function'

无法判断其他引用数据类型，必须使用instanceof来判断

#### 3.说一说你对闭包的理解？

闭包就是内部函数访问外部环境的自由变量，外部变量可以引用另一个函数的内部函数，通过这个内部函数的引用来访问他的上层作用域，如果这个外部变量依然引用着这个函数，所以不会被垃圾回收器回收，而导致内存泄露。闭包可以模仿块级作用域，能够实现函数柯里化，在构造函数中使用特权方法，Vue中数据响应式Observer使用了闭包

#### 4.说一说promise是什么与使用方法？

Promise是异步微任务，new Promise是同步执行，new Promise().then()是异步的，promise解决了异步多层嵌套回调，造成回调地狱的问题，让代码的可读性增加，更加容易维护。

promise的使用：通过new Promise()创建一个实例，传入一个包含resolve,reject作为参数的函调函数，执行成功是使用resolve(),执行失败是使用reject,promsie有三种状态，padding ,rejected,fulfilled,当状态发生改变的时候，就不会再更改，所以fulfilled状态执行resolve，rejected状态执行reject,然后会调用实例方法then(),then是异步调用，有两个回调函数，第一个是成功的回调，另外一个是失败的回调，失败的回调也可以链式调用使用catch传入失败的回调，promise.finally()不管成功还是失败都会执行，promsie中的类方法，Promsie.resolve()转换成fulfilled状态，并将参数传给then,Promise.reject()转换从rejected状态，并将给定的失败的信息传递给catch,any()方法会传入一个promise数组，只要有一个promise为fulfilled状态，则any也是fulfilled,只有全部为rejected则为rejected,allSettled()状态只有fulfilled状态，race()谁先改变状态就是使用谁的状态,all()只有全部为fulfilled才是fulfilled，有一个为reject则为reject

#### 5.说一说跨域是什么？如何解决跨域问题？

由于同源策略，当出现请求页面的地址和当前页面地址协议，域名，端口号有一个不一致就会出现跨域。

解决跨域的方法

使用jsonp进行请求数据，并且通过回调函数来携带返回的数据

cors：目前最常用的一种解决办法，通过设置后端允许跨域实现。
res.setHeader('Access-Control-Allow-Origin', '*');
res.setHeader("Access-Control-Allow-Methods", "GET, PUT, OPTIONS, POST");

node中间件、nginx反向代理：跨域限制的时候浏览器不能跨域访问服务器，node中间件和nginx反向代理，都是让请求发给代理服务器，静态页面面和代理服务器是同源的，然后代理服务器再向后端服务器发请求，服务器和服务器之间不存在同源限制

JSONP：利用的原理是script标签可以跨域请求资源，将回调函数作为参数拼接在url中。后端收到请求，调用该回调函数，并将数据作为参数返回去，注意设置响应头返回文档类型，应该设置成javascript。

postmessage：H5新增API，通过发送和接收API实现跨域通信。

webpack中配置devServer中的proxy实现跨域

#### 6.说一说BFC

BFC就是块级格式化上下文。他会形成一块独立的区域，不会影响边界以外的元素

BFC产生的条件

1. 根元素
2. 浮动元素（float:left/float:right）
3. 绝对定位元素(position:absolute,fixed)
4. 行内块元素 display:inline-block
5. 表格单元格 display:table-cell 表格的单元格默认就是
6. 匿名表格单元格元素
7. overflow计算值不是visible的块元素
8. display:flex/inline-flex的直接子元素
9. display:grid或者inline-grid的直接子元素
10. display:flow-root元素

作用

​	解决同一个BFC相邻两个box的margin会折叠的问题

​	解决浮动高度塌陷的问题，可以实现清除浮动的效果。通常是overflow:hidden

​		解决高度塌陷要满足以下条件

​			浮动元素的父元素触发BFC，形成BFC

​			浮动元素的父元素的高度是auto

​	BFC的高度是auto的情况下，高度的计算

​			如果只有inline-level 是行高的顶部和底部的距离

​			如果是block-level,是由最顶层块的上边缘到最底层块的下边缘之间距离

​			如果有绝对定位元素，将忽略

​			如果有浮动元素，则会增加高度包含浮动元素的下边缘

#### 7.说一说Vuex是什么，每个属性是干嘛的，如何使用 ？

VueX是全局状态管理的工具。vuex的属性有state,getter,mutations,action,module，state属性用来存储公共管理的数据，mutations属性定义可以修改state中的数据，是同步修改的，所以不要在mutation中书写ajax代码，这样数据不可追踪，getters属性可以认为是定义store的计算属性。getter的返回值会根据他的依赖被缓存起来，且只有当他的依赖发生了改变才会被重新计算，action中进行异步操作，可以操作mutation来修改数据，mutation是修改数据的唯一方法。module可以将store分为多个模块，每个模块都有自己的mutation，state,getter,action,module。通过store.state可以获取state,可以用计算属性中的mapState,store.commit来提交mutation,可以使用mapMutation来映射多个mutation,store,dispatch来分发action,可以使用mapAction来映射多个action  ,获取getter可以使用store,getter,可以使用maGetter来映射多个getter

#### 8.说一说JavaScript有几种方法判断变量的类型？

typeof 可以用来判断基本数据类型  还可以判断Function ,其他引用数据类型都是返回Object,null也是返回object

constructor可以判断引用数据类型 就是判断instance.__proto__.constructor=Function

instanceOf也可以判断引用数据类型 单数不能判断基本数据类型，就是运用原型链，判断右边的原型是否在左边的原型链上

Object.prototype.toString.call也可以用来判断引用数据类型

#### 9.说一说样式优先级的规则是什么？

样式的优先级应该分为4大类

第一类 !important的优先级最高，无论是什么引用方式，什么选择器

第二类是引用方式  行内样式优先级高于嵌入样式和外部样式，如果嵌入样式和外部样式使用相同的选择器，则看他们谁后引入，被覆盖

第三类是选择器

id选择器>类选择器，属性选择器，伪类选择器>元素选择器，伪元素选择器>通配符选择器

第四类是默认样式



 important 10000
 内联选择器 1000
 id选择器 100
 类选择器，属性选择器，伪类 10
 元素选择器，伪元素 1
 通配选择器  0

#### 10.说一说JS实现异步的方法？

得分点 回调函数、事件监听、setTimeout、Promise、生成器Generators/yield、async/await,

所有的异步代码都会在同步代码执行完后，从异步队列中取出依次执行。回调函数是最基本的异步函数，例如ajax请求，回调函数的有点是简单，容易理解和实现，缺点是不利于代码的阅读和理解，各个部分耦合度高，使得程序结构混乱，流程难以追踪(尤其是多个回调函数嵌套的情况，容易出现回调地狱)，而且每个任务只能指定一个回调函数，回调函数不能使用try..catch来捕获错误，不能直接·return  ,Promise包装了一个异步调用并且生成了一个promise实例，promise不经可以捕获错误，而且可以很好的解决回调地狱的问题，缺点是没办法取消promise,错误需要使用回调函数来捕获。generator函数是ES6提供的异步编程的解决方案，generator是一个状态机，封装了多个内部状态，可以暂停函数，yield可以暂停执行，next可以继续执行,每次返回都是yield的结果。优点是语义清晰，但是需要手动调用。async/await是基于Promises实现的，await会阻塞后面的代码的执行。如果多个异步代码没有依赖性却使用了await会降低性能

#### 11.说一说Vue2.0 双向绑定的原理与缺陷？

Vue的响应式原理：采用数据劫持和发布者-订阅者模式相结合的方式，遍历对象中的属性使用Object.defineProperty劫持各个属性的getter和setter方法，在数据发生变化的时候，触发setter,进而调用对应的notify方法执行依赖该属性的函数，进而更新页面的DOM。读取该属性的时候会触发getter，使用Object.defineProperty不能够监听对象的新增属性和删除属性。不可以监听通过数组索引来修改数据，不能够修改length,而监听到变化

#### 12.说一说数组去重都有哪些方法？

方式一：使用new Set(arr)进行去重，然后再使用Array.from()或者扩展运算符转成数组

方式二:	使用filter+indexOf来去重

方式三:   使用双重for循环+splice来进行去重

方式四:   使用[]+indexOf来去重

方式五:   使用sort去重，先进行排序，然后再进行当前索引和下一个索引值进行比较，push进新的数组

方式六:   使用对象属性不能相同的特点去重，以数组每一个item作为object的key,通过for循环判断obj[arr[i]]是否存在，如果不存在则push,并且obj[arr[i]]=1,否则就是obj[arr[i]]++

方式七：使用includes来去重，可以区别Nan,可以判断新的数组中是否包含这个值，不包含就push

方式八：使用hasOwnProperty来去重

方式九：先进行排序，然后在进行递归，splices删除重复数据

方式十：使用Map数据结构来去重，通过has判断，如果为false就push，并且set值，

方式十一：使用reduce+includes来去重，判断pre中是否存在

#### 13.说一说null 和 undefined 的区别，如何让一个属性变为null

**得分点 操作的变量没有被赋值、全局对象的一个属性、函数没有return返回值、值 `null` 特指对象的值未设置 undefined == null、undefined !== null** 

一个变量定义了，但是没有进行初始化，那他的值就是undefined。函数没有指定return的值，默认就是undefined。对象中某个属性不存在就是undefined，函数定义了形参，但是没有定义默认参数，实参就是undefined,undefined可以通过typeof 来判断是否undefined,undefined==undefined,undefined===undefined。null就是对象的值未设置，一个对象没有设置指针地址就是null,null通过typeof判断类型是object,null==null,null===null,null==undefined,null!==undeined,undefined表示未设置值的默认状态，null表示手动设置为空对象，儿不是默认状态，在实际使用过程中，变量不需要手动的赋值undefined,如果需要手动的释放对象，可以赋值null,typeof会判断为object,是因为js数据类型在底层都是以二进制的形式表示的，二进制的前三位为0会被typeof判断为objet,而null的二进制全是0，当对象被赋值为0以后就会被垃圾回收器择机回收并且释放内存

#### 14.说一下浮动

float属性可以指定一个元素向左或者向右浮动，浮动元素会脱离标准流

浮动规则一

​	元素一旦浮动后，脱离标准流

​			朝着向左或者向右方向移动，直到自己的边界紧贴者包含块(一般是父元素)或者其他浮动元素的边界为止

​			定位元素会层叠在浮动元素上面

浮动规则二

​	如果元素是向左或者向右浮动，浮动的元素的左右边界不能超过包含快的左右边界

浮动规则三

​	浮动元素之间不能够层叠，如果一个元素浮动，后面的元素会紧挨着前面的浮动元素排列，如果剩下的空间不够放下一个浮动元素则会向下移动，找到可以放下的空间

浮动规则四

​	浮动元素不能与行内级内容层叠，行内级内容会被推出去

浮动规则五

​	行内级元素，inline-block元素浮动后，其顶部将与所在行的顶部对齐

浮动的问题

​	由于浮动的元素脱离了标准流·，变成了脱标元素，所以不在向父元素汇报高度，父元素计算高度的时候不计算浮动元素的高度，所以会造成内容塌陷

解决高度塌陷的方法

​	方法一，给父元素设置固定高度。扩展性不好

​	方法二，给父元素最后添加一个空的块级元素，并且设置为clear:both。这样会增加无意义的空标签，维护麻烦，违反了结构与样式分离的原则

​	方式三，给父元素添加一个伪元素，::after,设置content:"",display:block，overflow:hidden,clear:both,visibility:hidden,height:0,   为了兼容IE6/7还要加上*zoom:1

#### 15.说一说es6中箭头函数？

箭头函数中没有this,箭头函数中的this指向是由上层作用域的中非箭头函数this决定的，箭头函数中没有arguments,如果箭头函数中出现arguments，则由上层作用域非箭头函数的arguments决定，箭头函数中arguments可以使用rest参数替代，箭头函数不可以作为构造函数，没有super()和原型,箭头函数不可以使用call,apply,bind显示绑定来改变this的指向，箭头函数不可以作为generator函数。箭头函数不能作为事件绑定的回调函数，如果使用了this，该this不会指向触发事件的元素，而是指向window。箭头函数适用于需要引用上层作用域的this,可以替代var _this=this,或者没有使用到this的函数

#### 16.说一说call apply bind的作用和区别？

call,apply,bind可以显示进行this的绑定，call可以传多个参数，并且立即执行函数，apply第二个参数传的是函数，也是会立即执行，bind可以传多个参数，并且会返回新的函数，call,apply还可以用于函数的执行。call,apply可以用于对象的继承，将伪数组转成真数组，进行原型链方法的借调，等等，bind可以用于react中this的绑定

#### 17.说一说this指向（普通函数、箭头函数）？

默认绑定this是指向全局window，普通函数的调用

隐式绑定this是指向调用的对象，对象调用方法时

显示绑定this指向绑定的值

new绑定this指向新创建的对象

箭头函数中没有this,this是由上层作用域中非箭头函数决定的

在严格模式下，this默认是undefined

默认绑定的优先级最低

显示绑定的优先级高于隐式绑定

new绑定的优先级高于bind

new绑定优先级高于隐式绑定

bind的优先级高于call/apply

new不可以和call/apply一起使用

setTimeout中this默认指向window

事件绑定中this指向绑定的元素

#### 18.说一说CSS尺寸设置的单位

px是绝对长度单位，em,rem,vw/vh是相对长度单位

em是在font-size中默认使用相对于父元素的字体大小,如果自己定义了font-size,则根据自身的font-size进行定义，其他属性也是相对于自身的font-size,

#### 19.说一说HTML语义化？

#### 20.说几个未知宽高元素水平垂直居中方法

#### 21.说一说JS变量提升？

函数和var 声明的变量在代码编译阶段会提升到最前面，但此时var声明的变量还没有赋值，所以此时访问是undefined,而函数可以在函数定义之前被调用，因为函数是一等公民，函数声明的提升优先级会比变量提升高，let,const不会出现变量提升，let const在定义以前访问会出现暂时性死区

#### 22.说一说 HashRouter 和 HistoryRouter的区别和原理？

HashRouter  和 HistoryRouter都是利用浏览器的两种特性来实现前端路由的，根据这两种特性，可以实现不需要刷新页面的去修改内容。HashRouter就是利用通过监听window.onhashchange根据location.hash的变化来修改Dom,url中hashRouter和historyRouter的不同是，hash会在url中有#这样的hash值，而history没有。historyRouter是根据h5新增的历史调用栈API来实现的，他有六种模式来改变url而不会重新刷新页面

history.replaceState():替换原来的路径

history.pushState() 使用新的路径

history.popState()  路径的回退

history.back() 向后改变路径

history.go() 跳转至那个路径

通过window.onpopState来监听浏览器的前进和后退，pushState和replaceState都是可以携带参数，hash可以兼容低版本的浏览器，如果想传递任意类型的数据到记录栈可以使用history,history需要后端配合，如果后端不配合，就会出现404，而hash不需要

#### 23.说一说map 和 forEach 的区别？

得分点 map创建新数组、map返回处理后的值、forEach()不修改原数组、forEach()方法返回undefined

map会返回新的数组，forEach不会修改原来的数组，map需要return foreach不需要return,foreach中return是无效的，map中this默认指向undefined,foreach中this默认指向window,forEach不能终端执行，只能够抛出异常来终止执行。forEach没有返回值新的数组所以不能够进行链式调用，map的处理数组比foreach快

#### 24.说一说事件循环Event loop，宏任务与微任务？

js代码执行过程中，同步代码先执行，要到定时器，就会先将定时器先挂起，继续向下执行，执行到异步代码，就会将异步代码进入事件队列，宏任务就会进入宏任务队列，微任务就是进入微任务事件队列，同步代码执行完后，就会从事件队列中取出异步代码，进入执行上下文调用栈，微任务先执行，执行完后在执行宏任务，等定时器到期之后也会加入到事件队列，就这样不断的循环往复执行，就是事件循环，事件循环包括浏览器的事件循环和Node的事件循环，浏览器的事件循环中宏任务包括定时器，ajax请求，window.postmessage,setImmediate,UI render,Dom监听，微任务包括promise的then回调，async/await,Mutation Obderver，queueMicrotask。Node的事件循环中微任务为process.nexttick,promise的then回调，queueMicrotask,宏任务队列包括，定时器，ID事件，setImmediate,close事件

#### 25.说一说Vue3.0 实现数据双向绑定的方法 ？

vue3中实现数据双向绑定采用new Proxy来替代Object.defineProperty实现，proxy可以监听整个对象，使用new proxy()将需要监听的对象作为参数传递，并且有13中拦截器，可以对对象以及函数进行监听，例如监听对象属性的getter,setter,delete操作,in操作，defineProperty操作等，可以解决object.defineproperty不可以监听对象属性的新增和删除操作，不可以监听数组通过索引赋值，和手动给length赋值等操作

#### 26.说一下Diff算法？

得分点 patch、patchVnode、updateChildren、vue优化时间复杂度为O(n) 

diff算法中的比较过程，第一步：patch函数首先会对新旧节点进行比较，如果旧的节点存在且和新的节点类型不同，则会销毁旧的节点。如果旧节点存在，如果新旧节点类型相同时，然后对新的节点的类型进行判断，如果是文本节点，则直接替换成文本节点，如果是注释节点也是直接的插入。处理静态节点，处理fragment组件节点等，然后就是处理普通的Dom元素和组件节点等等，在处理普通元素的时候，他会先判断旧节点是否存在，不存在则直接挂在。否则调用patchElement函数。在执行patchElement的时候首先处理props的情况，先将新的节点的props全部挂载到el上，判断旧节点的props是否不需要在新的节点上，如果不需要，那么删除对应的属性。然后通过调用patchChildren来对比子元素，如果新的节点是一个字符串类型，则直接调用el.textContent=newChildren,如果新节点不是字符串类型，旧节点是字符串类型，那么直接遍历新的节点，挂载到el上，如果新旧节点都是数组，则会分为有key和没有key两种，有key则调用patchKeyedChildren方法，没有key则调用patchUnkeyedChildren方法。调用patchUnkeyedChildren方法时，会获取到长度最小的那个节点，for循环依次进行patch，patch完之后，剩下的节点，如果旧的长度大于新的，则移除这些节点，如果新的长度大于旧的长度则创建新的节点，如果有key则会调用patchKeyedChildren方法，首先节点会从头部开始比较，如果两个节点相同则会调用patch,并且i++,如果节点不同则会跳出循环，然后再从尾部进行比较，重复相同的步骤，并且e1--,e2--,如果i>e1而且i<e2,就是旧的节点遍历完了，依然有新的节点，则进行挂载，如果新的节点遍历完，旧的节点还有则移除旧的节点。对于剩下的未知的序列，就是无须的序列，首先会根据key建立map索引图， 遍历剩下的旧节点, 新旧对比, 移除不使用的旧节点，oldIndex = 0 是一个特殊值，表示新节点没有对应的旧节点。用于确定最长稳定子序列，建立新的vnode的key的位置在旧的vnode的映射，然后for遍历旧节点中还没有被对比的节点，如果该节点已经被patch过，则卸载掉，如果在旧的vnode中key存在，在获取相同的key在新vnode的位置，如果存在，则新节点在旧节点的映射中设置所以旧节点的索引位置，然后进行新旧节点的patch,如果在旧node中key不存在，试图定位一个相同类型的没有key的节点的位置，准备用这个替换成新的值，从新结点中找到类型相同，且在旧节点没有对应映射的节点，设置进去，如果在新的vnode中没有对应的映射，就是在新的vnode中不存在这个key了，就卸载掉

#### 27.说一说三栏布局的实现方案

圣杯布局，双飞翼布局

#### 28.说一下浏览器垃圾回收机制？

#### 29.说一说 vue 的 keep-alive ？

#### 30.CSRF攻击是什么？

#### 31.XSS攻击是什么？

#### 32.说一说js继承的方法和优缺点？

#### 33.说一说defer和async区别？

默认情况下浏览器下载完js代码会立即执行，如果js需要操作dom,则会阻塞页面的渲染，async属性可以使加载js文档和dom树的构建同时进行，当js加载完成，js代码执行，会阻塞html的渲染，defer会等到dom构建完成以后再加载js代码。

defer是js的下载和执行不阻塞DOM tree的构建，defer中的js代码执行的时候Dom构建完成，defer会在DomcontenLoad之后执行，多个设置defer的script会按顺序加载，defer仅适用于外部脚本，对script的内容会忽略。而async不能保证顺序执行，不会阻塞DOM渲染，不能保证DOMContentLoaded之前或者之后执行，async用于独立脚本执行，就是那些不需要依赖外部文件的脚本

加分项：阻塞的原因：由于js是可以操作dom的，如果在修改这些元素的时候同时渲染界面即js线程和ui线程同时运行，那么渲染线程前后获得的数据可能不一致，因此为了防止渲染出现不可以预测的效果，所以浏览器设置GUI渲染线程和js引擎为互斥的关系当浏览器执行js代码的时候渲染线程就会被保存在一个队列中，直到js程序执行完成才会接着执行。如果js执行事件过长，就会造成渲染界面过程不连贯，导致页面渲染加载阻塞的感觉

#### 34.说一下浏览器如何渲染页面的

浏览器首先会解析页面，把html构建成DOM树，css构建成cssom树，css的加载不会阻塞dom树的构建，然后将dom树和cssom树整合成render树，如果加载到script标签，会阻塞页面的渲染进程，所以需要把script放在底部，然后执行js代码，经过布局和绘制，最终渲染到屏幕上

#### 35.说一说vue computed和watch的区别？

当需要依赖其他状态来产生新的状态的时候，可以使用computed,computed适用于计算比较多的场景。computed依赖于其他状态，computed是同步的，而且有缓存，如果依赖的状态没有发生变化，他不会重新计算，computed默认是调用getter方法，也可以修改数据调用setter方法，watch是作为侦听器，可以监听data，props,emit等属性的变化，也是有惰性的，watch在数据初始化的时候不会执行，只有在数据变化的时候才会执行，可以设置immediate:true来让watch立即执行一次，watch默认不能够监听对象的属性的变化，可以设置deep:true,对对象进行深度监听

#### 36.说一说 Vue 中 $nextTick 作用与原理？

得分点 异步渲染、获取DOM、Promise 

将回调延迟到下一次DOM更新循环之后执行，在修改数据之后立即执行它，然后等待DOM更新

ue更新DOM是异步执行的，在修改数据后，视图不会立刻更新，而是等待同一事件循环中所有数据变化之后，同意更新视图，所以修改完数据，立刻获得的是未修改的dom,nextTrick就可以在DOM更新之后执行回调函数，解决异步渲染不可以获取到更新之后的DOM的问题，nextTick会返回一个promise,所以可以使用async/await

应用场景：在钩子函数created()里面想要获取操作Dom，把操作DOM的方法放在$nextTick中

#### 37.说一说new会发生什么？

1. 创建一个空对象
1. 让这个空对象的[[prototype]]属性指向构造函数的prototype
1. 让构造函数中的this指向新创建的对象
1. 执行该构造函数
1. 如果构造函数没有返回对象则返回新创建的对象

#### 38.说一下token 能放在cookie中吗？

得分点 能、不设置cookie有效期、重新登录重写cookie覆盖原来的cookie 

token一般用来判断用户是否登录，它内部包含的信息包括uid(用户唯一的身份标识)，time(当前的时间戳)，sign(签名，token的前几位是以哈希算法压缩成的一定长度地十六进制字符串)，token可以存放在cookie，token是否过期由后端决定，所以token存储在cookie中只要不设置cookie的过期时间就可以，如果token失效，只要返回固定状态表示token失效，需要重新登录，重新设置cookie中的token就可以了，

#### 39.说一下浏览器输入URL发生了什么？

#### 40.说一说组件通信的方式？

#### 41.说一说 v-if 和 v-show区别？

v-if是条件渲染，是惰性的，只有在条件判断为true的时候才会渲染到页面中去，v-show相当于设置display来进行切换，所有v-if有更高的切换开销，v-show有更高的切换开销，如果需要频繁切换，需要使用v-show，v-else和template不可以用于v-show，v-show无论如何它的DOM实际上都是存在的，只是通过CSS来切换display属性，当v-if当条件为false时，其对应的元素不会渲染到DOM中

#### 42.React生命周期的各个阶段是什么？

#### 43.React组件间传值的方法有哪些？

父组件向子组件传值是使用props进行传递，

子组件向父组件传值，可以使用回调函数，父组件向子组件传递回调函数，子组件执行回调函数并传递参数

子孙组件之间的传递可以使用createContext()

兄弟组件间的数据传递可以使用事件总线来传递

还可以使用redux进行全局状态管理

#### 44.说一说盒模型？

盒模型分为IE的盒模型和W3C的盒模型，W3C的盒模型指的是content的宽高，IE的盒模型是将content,padding,margin包含在宽度和高度之内，可以使用css ,box-sizing:border-box来将W3C的盒模型转换成IE盒模型，默认是content-box

#### 45.说一说伪数组和数组的区别？

得分点 类型是object、不能使用数组方法、可以获取长度、可以使用for in遍历

数组的类型是Array,类数组的类型是object,类数组可以使用length查看长度，可以使用索引来获得值，但是不可以修改length,不能使用array的方法，可以使用forin进行遍历，类数组常用的场景：函数参数argumenrs,NodeList等，类数组转成数组方法，Array.from(arr),Array.prototype.slice.call(arr),索引不连续时会自动补位

#### 46.说一说如何实现可过期的localstorage数据？

#### 47.说一说axios的拦截器原理及应用？

#### 48.说一说创建ajax过程？



#### 49.说一下fetch 请求方式？

#### 50.说一下有什么方法可以保持前后端实时通信？

#### 51.说一下重绘、回流区别如何避免？

对节点的大小，位置的修改重新计算被称为回流，例如DOM结构的改变(添加新的节点或者删除节点)

改变布局，例如修改了width,height,padding,font-size等值

窗口resize、(修改了窗口的尺寸)

调用getComputedStyle方法获取尺寸，位置信息等

修改背景色，字体颜色，边框颜色，边框样式等，

回流一定会引起重绘，重绘不一定会引起回流，所以应该减少回流

1.修改样式尽量一次性修改，例如通过cssText,classList,class来修改

2.避免频繁的操作DOM，可以是用document.documentFragment批量修改片段

3.避免使用getComputedStyle频繁获取计算值，如果需要，可以将计算值存储起来

4.修改DOM可以先设置diaplay:none，是他不存在render树，再进行修改，修改完成后再加入render树中

5.对某些元素使用position:absolute,fixed使其脱离标准流，这样开销会比较小

6.默认情况下，标准流中的内容都会被绘制在同一个图层上，利用一些属性创建一个新的合成图层，这些layer可以使用GPU加速绘制，因为这些图层都是单独渲染，减少了回流和重绘，例如3d transform,vedio,canvas,iframe,opacity动画转换，position:fixed,animation或者transition设置了opacity,transorm.分层确实可以提高性能，但是不要过度使用，她是以内存为代价的

#### 52.说一说 Vue 列表为什么加 key？

得分点 性能优化、diff算法节点比对、key不能是index

key属性主要时用在Vue的虚拟DOM算法，在新旧Vnode对比时辨识vnodes,如果不使用key,vue会使用一种最大限度减少动态元素并且尽可能减少的尝试就地修改/复用相同类型元素的算法，而使用key，他会基于key的变化重新排列元素顺序，并且移除或者销毁不需要的元素

相同的父元素的子元素的key必须唯一，如果有重复的可以会渲染错误

如果使用index作为key,没有优化效果，因为index会改变

它也可以用于强制替换元素或者组件而不是重复使用它时。当你遇到如下场景时它可能会很有用：

- 完整地触发组件的生命周期钩子

- 触发过渡

#### 53.说一说vue-router 实现懒加载的方法？

```
vue实现懒加载的两种方法
component：resolve=>require(["./app.vue"],resolve)
component:()i=>import(/*webpackChunkName:app*/"./app.vue")

webpackChunkName可以不写
不要在路由中使用异步组件。异步组件仍然可以在路由组件中使用，但路由组件本身就是动态导入的。
webpack会进行代码分隔，进行独立打包，当使用道德时候才会加载，vue-router只会第一次加载时获取，以后都会使用缓存
```

#### 55.ReactRouter基本用法是什么？

#### 56.setState是同步还是异步的？

setState在组件生命周期或者react合成事件中，setState是异步的

在原生的dom事件中，setTimeout中，setState是同步的

setState设计为异步，可以显著提升性能，如果每次调用setState都进行更新，那么意味着render函数会被频繁的调用，页面会重新渲染，这样效率很低，最好的办法因该是活得多个更新，然后进行批量更新，

如果同步进行更新state,但是还没有执行render函数，那么state,props不能保持同步，state和props不一致，在开发中会产生很多问题

#### 57.React事件绑定原理

得分点 非原生事件、SyntheticBaseEvent 

React中的事件不是原生的事件，而是对原生的event进行封装的新类SyntheticBaseEvent，模拟处DOM事件的所有功能,通过event.nativeEvent可以获得原生事件，react将所有的事件都绑定在root根组件上，之前都是绑定在document上的，react中的事件和DOM事件不一样，和vue也不一样，react并不是将click事件绑定在该div的真实DOM上，而是在root处监听所有支持的事件，当事件发生并冒泡到root处时，react将事件内容封装并交给真正的处理函数运行，这样的方式不仅减少了内存的消耗，还能再组件挂载销毁时统一订阅和移除事件，另外冒泡到root上的事件也不是原生浏览器事件，而是React自己实现的合成事件(SyntheticBaseEvent),因此我们如果不想要事件冒泡的话，调用event.stopPropagation是无效的，应该调用event.preventDefault

#### 58.React中hooks的优缺点是什么？

#### 59.说一说前端性能优化手段？

#### 60.说一说性能优化有哪些性能指标，如何量化？

#### 61.说一说服务端渲染？

#### 62.事件扩展符用过吗(...)，什么场景下？

1.在数组调用时可以替代数组的apply方法

2.用于合并数组

3.与解构赋值相结合，生成新的数组

4.将字符串转成数组

5.任何iterator对象，都可以通过展开运算符转成数组

6.用于数组的浅拷贝

7.构造字面量对象可以使用展开语法

8.在 new 表达式中应用，使用 `new` 关键字来调用构造函数时，不能**直接**使用数组 + `apply` 的方式（`apply` 执行的是调用 `[[Call]]` , 而不是构造 `[[Construct]]`），可以使用展开语法替代

#### 63.说一说vue钩子函数？

组件从创建，挂载，更新，销毁的全过程就是vue的声明周期，包括beforeCreate,created,beforemount,mounted,beforeUpdate,updated,beforeumount,umouted,

beforeCreate钩子函数会在实例初始化之后，进行数据侦听和事件/侦听器的配置之前同步调用，

created钩子函数会在组件实例被创完成后被立即同步调用，此时$watch,computed,method,data等属性可以被调用，但是挂载阶段还没开始，所以$el属性还不可以使用，

beforeMount钩子函数被调用时，该钩子函数会在挂载前调用，render函数首次被调用，在服务器端渲染期间不会被调用

mounted钩子函数被调用时，在实例挂载完成后调用，这时候传给app.mount的元素已经被新创建的vm.$el替换，mounted不会保证所有子组件都被挂载完成。如果想执行在这个视图渲染完毕后执行代码，可以在mounted内部使用vm.$nextTick

```js
mounted() {
  this.$nextTick(function () {
    // 仅在整个视图都被渲染之后才会运行的代码
  })
}
```

该钩子函数不会再服务端渲染时调用

beforeUpdate钩子函数是在数据发生变化之后DOM更新之前调用，适合在DOM更新之前访问它，例如移除手动添加的事件监听器。**该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务器端进行。**



updated钩子函数会在数据更新之后虚拟DOM重新渲染完毕之后被调用，此时DOM已经更新完毕可以进行DOM操作，但是不建议下该狗子中进行状态的改变，通常使用watch或者computed来进行状态监听，updated不能保证子组件已经挂载完成，如果想要在组件完全渲染以后操作可以使用vm.$nextTick.该钩子函数不能用于服务端。

activited钩子函数

该钩子会被keep-alive缓存的组件激活时调用，该钩子在服务器管渲染期间不被调用

deactivated钩子函数会在被keep-alive缓存的组件失活时调用，在服务端不可用

beforeMount钩子函数会在组件卸载之前调用，在这个时候1可以解除对事件的监听，**该钩子在服务器端渲染期间不被调用。**

mounted钩子函数调用时，组件已经卸载完成，指令和监听器都卸载完成，**该钩子在服务器端渲染期间不被调用。**

父子组件的声明周期

挂载阶段

父beforeCreate->父created->父beforMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted

更新阶段

父beforeUpdate->子beforeUpdate->子updated->父updated

销毁阶段

父beforeDestory->子deforeDestory->子destoryed->父destoryed

#### 64.vue生命周期的源码实现

#### 65.nexttick源码

#### 66.v8垃圾回收

#### 68.计算机网络模型有哪些？具体每一层干什么的，传输的数据是什么，有哪些协议？

#### 69.进程与线程的区别？（暂定）

1. 一个线程属于一个进程，而一个进程可以有多个线程，但至少有一个线程，线程依赖进程的存在
2. 进程在执行过程中拥有独立的内存单元，而多个线程共享进程的内存
3. 进程是资源分配的最小单位，线程是CPU调度的最小单位
4. 进程的切换开销远大于线程的切换开销
5. 进程间的通信IPC，线程间可以直接写进数据段(如全局变量)来进行通信--需要进程同步和互斥手段的辅助，以保证数据的一致性
6. 进程编程调试简单可靠性高，但是创建销毁开销大，线程正好相反，开销小，切换速度快，但是编程调试相对复杂
7. 进程间不会相互影响，一个线程挂掉会导致整个进程挂掉
8. 进程适合多核，多机分布，线程适用于多核

进程：我们可以认为，启动一个应用程序，就会默认启动一个进程(也可能是多个进程)

线程：每一个进程中，都会启动至少一个线程用来执行程序中的代码，这就主线程，所以说进程是线程的容器

操作系统类似于一个大工厂，工厂中有很多车间，这个车间就是进程，每个车间有可能有一盒以上的工人在工厂，这个工人就是线程

js是单线程的，js线程有自己的容器进程，浏览器或者node

#### 70.基本数据类型之间的转换
1.console.log(null==undefined)//true
2.非空字符串通过Boolean()转成true,空字符串转成false，对于Number类型，非0数字都会转成true,0,NaN会转成false,对象都会被转成true,undefined会被转成false，if条件判断会自动转成boolean进行判断，使用!!也可以进行隐式转换
3.数值类型的转换，可以通过Number(),parseInt(),parseFloat()三个函数显示的进行转换，Number()可以用于任何数据类型，parseInt和parseFloat主要用于字符串，Number()进行类型转换时，
boolean值false会转成0，true会转成1，null会转成0，undefined会返回NaN,对于字符串的转换，如果字符串中只包含数字则会转换成十进制的数字，如果字符串中包含浮点数，则会转成浮点数，如果包含十六进制的字符串则会转成十进制的数字，如果是空字符串则会转成0，如果字符串中包含其字符，则会转成NaN。对象会先调用valueOf按照上述方式进行转换，如果是NaN则会再调用toString再进行上述的转换。
使用parseInt进行类型转换时，他会从第一个非空格的字符进行转换，如果第一个字符不是加减，数字字符，则会转成NaN,空字符串也会转成NaN,如果第一个字符时加减，数字则会一直到非数字字符位置，parseInt会进行截断字符，如果是浮点数字符串，则会遇到.就会截断
parseFloat只能解析十进制，会忽略第二个.他会忽略字符转以0开头的值
4.转成字符串，+""就可以变成字符串，也可以调用toString来转换，除了null和undefined都可以toString，使用String()也可以转成字符转，有toString则会调用这个方法，null则会装成"null",undefined=>'undefined'

#### 71.webpack 的 plugin 和 loader 的区别和原理

#### 72.静态资源一般采取什么缓存策略？

强缓存和协商缓存

#### 73.移动端300ms的延迟问题，问什么会有这个问题

#### 74.vue模版编译都有哪些阶段

#### 75.vue虚拟dom怎么生成的

#### 76.Vue2 3区别

#### 77.手写防抖节流

#### 78.手写dom树转js对象

#### 79.给一张上下颠倒的照片前端如何将图片位置旋转矫正发送给后端

#### 80.如果一个页面打开很慢，可能是哪些原因，怎么进行优化

#### 81.TCP的三次握手，以及每次的作用

#### 82.TCP和UDP的区别，TCP的可靠性如何保证，粘包如何处理，流量控制，滑动窗口原理

#### 83.Vue的响应式原理

#### 84.Vue中computed和data里的值的联系，computed的特点以及响应式变化

#### 85.Vue3与Vue2相比有哪些变化，Vue2中怎么监听数组变化，proxy 和 defineProperty 的比较

详情请看vue3和vue2区别

vue2中重写数组中的方法，主要分为这几步

1. 获取到Array原型链上的方法，因为拦截后还需要调用原生的方法进行操作
2. 使用object.defineproperty拦截数组的操作
3. 再把array的实例原型指向修改后的实例原型



proxy和defineproperty的区别

defineproperty的作用主要不是用来监听对象属性的变化，defineproperty不可以监听数组的变化，必须重写数组的方法，会对对象进行修改，不能够监听对象属性的修改和删除，不可以监听数组手动修改length和通过数组下标修改值，proxy代理，可以代理对象，不需要修改元素组，而且有十三中捕获器，还可以配合reflect来使用

#### 86.webpack的打包过程，loader和plugin的区别，怎么对打包体积进行优化等

#### 87.window.onload和DOMContentLoaded的区别

#### 88.了解http2

#### 89.网页布局的方式有哪些

#### 90.使用promise封装ajax

#### 91.写一个vue的自定义指令

#### 92.vuex的原理，数据存储在哪里

#### 93.说一下href和src的区别

#### 94.说一下你知道的webpack打包构建的优化

#### 95.说一下你了解的plugin和loader,以及如何去写一个plugin和loader

#### 96.说一下transform的原理(先平移再旋转和先旋转再平移有什么区别)

#### 97.函数和对象及其上下文存储在哪里

#### 98.手写虚拟dom转换成真实的dom

#### 99.为什么一个网站的资源会存储在多个域名中

#### 100.如何实现前端的权限控制

#### 101.说一下vue3相对于vue2的优化

#### 102.说一下vite相对于webpack的优化

#### 103.实现vue中template生成虚拟dom

#### 104.知道http499是什么

表示客户端主动关闭连接，即HTTP 499 Client Closed Request,HTTP 499 表示nginx使用非标准状态码。表明当nginx正在处理请求时，客户端关闭了连接

#### 105.说一说mata标签的作用

meta元素用来定义元数据的，meta标签用于定义不能用其他元数据元素定义的元数据信息，header中定义元数据的标签有style,link,title等，不能用这些标签定义的数据，就写在meta中。

meta元素定义的元数据的类型包括以下几类

如果设置了charset meta元素就是一个字符集声明，告诉文档使用那种字符编码

如果设置了http-equiv属性，meta元素则是编译指令  主要是做兼容性处理，告诉IE浏览器去模仿那个浏览器的行为，IE模仿edge的行为，chrome使用1

```
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
```

如果设置了name属性，meta元素提供的是文档级别的元数据，应用于整个页面

#### 106.说一下css的优先级，important的缺点

css的优先级是按照css的权重来叠加的，

！important  10000

内联样式 1000

id选择器 100

类选择器，属性选择权，伪类  10

元素选择器，伪元素 1

通配符 0

通过各项叠加来计算css的优先级

important的优先级最高，在全局和组件中要谨慎使用，在对一些继承属性使用important，在修改这个属性的时候需要再次添加important，可能会造成混乱

#### 107.说一下响应式布局

#### 108.写一个返回数据类型的函数，要求自定义的类实例化的对象返回定义的类名

#### 109.说一下js的事件循环和node事件循环的区别

js的事件循环是运行在浏览器环境中的，node事件循环是运行在node环境中的

浏览器的事件循环是一个我们编写的javascript代码和浏览器api调用(setTimeout/ajax/事件监听)的桥梁，桥梁之间他们用回调函数进行沟通

node事件循环是一个我们编写的javascript代码和系统调用(file syatem network)之间的桥梁，桥梁之间通过回调函数进行沟通

浏览器的事件循环：

​	首先会在函数执行调用栈中执行同步代码，如果执行到定时器会先将定时器挂起，继续向下执行执行到微任务代码，则先进入微任务队列，执行到宏任务，则进入宏任务队列，同步代码执行完成后，则会开始执行异步队列中的代码，先执行微任务队列，微任务队列之星完后在执行宏任务队列，等定时器执行完后，则进入宏任务队列执行，如此往复直到执行完成

宏任务队列：ajax，setTimeout ，setInterval，DOM监听，UIrendering等

微任务队列：Promise的then回调，Mutation observer api queueMicrotask() async/await等

node的事件循环是由libuv来实现的，libuv采用的就是非阻塞异步IO的调用方式

node事件循环分成很多个阶段

定时器(Timer):setTimeOut ，setIterval

待定回调（pending callback） 对某些系统操作（如TCP错误类型）执行回调，比如TCP连接时接收到 ECONNREFUSED

idle,prepare:进内部使用

轮询：检索新的I/O事件，执行与IO有关的回调

检测：setImmediate()回调函数在这里执行

关闭回调函数:一些关闭的回调函数，如socket.on('close',)

node的事件的循环更加复杂，他也分为微任务和宏任务

宏任务：setTimeout,setInterval,IO事件，setImmediate,close事件

微任务：promise.then,async/awiat,process.nextTick,queueMicrotask

Node中的事件循环不只是 微任务队列和 宏任务队列：

​    微任务队列： 

​		next tick queue：process.nextTick； 

​		other queue：Promise的then回调、queueMicrotask；

​    宏任务队列： 

​		 timer queue：setTimeout、setInterval； 

​		poll queue：IO事件；

​		check queue：setImmediate； ü close queue：close事件

执行宏任务的时候都必须先判断微任务队列是否有值

#### 110.实现一个柯里化函数（写代码）

#### 111.实现函数的链式调用（写代码，compose函数）

#### 112.如何配置less文件的loader

```
test:/\.less$/,
use:[
style-loader,css-loader,postcss-loader,less-loader
]
```



#### 113.webpack是如何做到一步步处理loader文件的

#### 114.为什么webpack是自上而下执行的

#### 115.实现防抖和节流函数（写代码）

#### 116.如何用css实现两栏布局

#### 117.说一下css的position定位

运用position进行定位，常取得5个值是：	static relative absolute fixed  sticky

position的默认值就是static

使用其他属性值可以让position变成定位元素

relative:会按照标准文档流进行布局，可以通过left right top bottom 来进行定位，参照对象是元素原来的位置

fixed：元素会脱离标准流，可以通过left，right，top，bottom进行定位，参照对象是视口。例如画布滚动时，元素不动

absolute：元素会脱离文档流，可以通过left，right，top，bottom进行定位，参照对象是最近定位（position不为static的元素）的祖先元素，否则就会相对于视口进行定位



#### 118.说一下js的作用域

js的作用域有全局作用域，函数作用域，eval作用域，块级作用域

全局作用域就是最顶层的作用域，所有变量都可以访问到，函数作用域就是每个函数内部的作用域，eval作用域就是eval函数内部的作用域，es6新增了块级作用域，块级作用域是函数作用域的子集

#### 119.说一下js的import和node的require的区别

#### 120.说一下你了解的js的数组的常用方法

数组常用的方法有

Array.prototype.join()

Array.prototype.concat()

Array.prototype.slice()

Array.prototype.splice()

Array.prototype.push()

Array.prototype.shift()

Array.prototype.unshift()

Array.isArray()

Array.prototype.pop()

Array.prototype.map()

Array.prototype.forEach()

Array.prototype.reduce()

Array.prototype.sort()

Array.prototype.some()

Array.prototype.every()

Array.prototype.entry()

Array.prototype.find()

Array.prototype.findIndex()

Array.prototype.flat()/flatMap()

Array.prototype.indexOf()

Array.prototype.includes()

Array.from()

Array.of()

Array.prototype.toString()

Array.prototype.values()

#### 121.说一下GET和POST的区别

- GET在浏览器回退时是无害的，而POST会再次提交请求
- GET请求会被浏览器主动缓存，而POST不会，除非手动设置
- GET请求参数会被完整保留在浏览器的历史记录里，而POST中的参数不会被保留
- GET请求在URL中传送的参数是有长度限制的，而POST没有限制
- GET参数通过URL传递，POST放在Request body中
- GET请求只能进行 url 编码，而POST支持多种编码方式
- GET产生的URL地址可以被收藏，而POST不可以
- 对参数的数据类型，GET只接受ASCII字符，而POST没有限制
- GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息

#### 123.说一下px，rem，em的区别和联系

px是绝对长度单位，rem，em是相对长度单位，em是相对于元素本身的font-size来进行定位的，如果本身没有font-size则会继承自父元素的font-size，rem是根据根元素的font-size

#### 124.说一下instanceof的原理

通过判断instance的[[prototype]]是否和原型链上的原型是否匹配

#### 125.说一下this的指向有几种方式

1.默认绑定  this默认是指向全局，浏览器中this指向window,node中this指向global  在由于兼容性考虑可以使用globalThis  在函数声明中，this就是默认绑定

2.隐式绑定  在对象中this是指向调用的变量

3.显示绑定   可以使用call ,apply ,bind来指定this的指向

4.new绑定   使用new创建的对象，构造函数中的this会指向新创建的对象

#### 126.说一下v-model的原理

v-model就是如下的语糖

对于input，textarea元素

```
<input
  :value="text"
  @input="event => text = event.target.value">
```

对于文本元素text，textarea会绑定value property监听input事件，checkbox和radio会绑定checked property 监听change事件，select属性会绑定value属性，监听change事件

#### 127.如何封装一个弹窗组件，需要考虑那些问题

#### 128.说一下keep-alive的原理，以及如果超出缓存长度，如何处理

#### 129.说一下你对jwt认证机制的理解，它的优缺点

#### 130.说一下new创建实例的时候都做了什么

new创建实例的过程

1. 创建一个空对象
2. 新创建的对象的[[prototype]]指向构造函数的prototype
3. 构造函数的this指向新创建的对象
4. 执行构造函数
5. 如果构造函数没有返回值，则返回新创建的对象

#### 131.说一下你对闭包的理解

#### 132.说一下你对flex布局的理解，以及其中属性的默认值(未完待续)

flex是用于按行或者按列布局元素的一维布局方法，元素可以通过膨胀以填充额外的空间以适应更小的空间。flex布局可以解决原来布局的一些痛点，例如垂直居中块元素，例如使容器的所有子项等分可用宽度，而不用管具体的宽高，在多列布局中所有列都采用相同的高度，即使他们包含的内容量不同。

开启了flex布局的元素叫flex item

flex container 里面的直接子元素叫做flex item

当flex conteainer中的子元素变成flex item时，具备以下特点：

​	flex item的布局将受flex container属性的设置来来控制和布局

​	flex item不再严格区分块级元素和行内级元素

​	flex item 默认情况下是包裹内容的，但是可以设置宽度和高度

display：flex或者inline-flex可以变成 flex-container

flex container的css属性有

​	flex-flow

​	flex-direction

​	flex-wrap

​	flex-flow

​	justify-content

​	align-items

​	align-content

应用在flex items上的c s s属性

​	flex-grow

​	flex-basis

​	flex-shrink

​	order

​	align-self

​	flex

#### 133.说一下ES6的块级作用域（待补充）

let const function class声明都会形成块级作用域，块级作用域可以让变量必须先声明再访问，否则会出现暂时性死区。块级作用域不会出现变量提升。例如在setTimeOut中的通过递归获取dom节点，可以使用该特性来代替立即执行函数

#### 134.说一下async和await的原理，generator用来做什么

async和await就是generator和promise.then的语法糖

#### 135.如何用ES5实现ES6的class的static和private

static:ES5中是直接在构造函数中添加

private:默认使用_来替代，并且定义get set方法

#### 136.用promise封装实现readfile和writefile的同步请求（写代码）

#### 137.手写ES6的模板字符串（写代码

