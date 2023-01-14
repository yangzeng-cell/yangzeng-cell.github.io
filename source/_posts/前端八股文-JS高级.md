---
title: 前端八股文-JS高级
date: 2022-09-04 10:50:04
tags:
- [javascript]
categories:
- [前端八股文]
---

# this绑定

## this的绑定规则有⼏种

默认绑定：⽴函数调⽤，函数没有被绑定到某个对象上进⾏调⽤

隐式绑定：通过某个对象发起的函数调⽤，在调⽤对象内部有⼀个对函数的引⽤。

显式绑定：明确this指向的对象，第⼀个参数相同并要求传⼊⼀个对象

​	apply/call

​	bind

new绑定：

​	创建⼀个全新对象

​	新对象被执⾏prototype链接

​	新对象绑定到函数调⽤的this

​	如果函数没有返回其他对象，表达式会返回这个对象

## this的⾯试题解析

```js
var name = "window";
function Person(name) {
this.name = name;
this.foo1 = function () {
console.log(this.name);
 };
this.foo2 = () => console.log(this.name);
this.foo3 = function () {
return function () {
console.log(this.name);
 };
 };
this.foo4 = function () {
return () => {
console.log(this.name);
 };
 };
}
var person1 = new Person("person1");
var person2 = new Person("person2");
// person1.foo1() // person1 隐式调⽤
// person1.foo1.call(person2) // person2 显示调⽤ this指向person2所在的对象
// person1.foo2(); // person1 箭头函数 向上层作⽤查找 上层作⽤域中的this为person1指向的对象
// person1.foo2.call(person2); // person1 箭头函数 显示绑定没⽤
// person1.foo3()() // window 相当于将返回的函数赋值给⼀个变量 指向该变量 是ᇿ⽴函数调⽤
// person1.foo3.call(person2)() // window 默认调⽤
// person1.foo3().call(person2) // person2 将函数的this显示绑定到person2所在的对象
// person1.foo4()() // person1 箭头函数 向上层作⽤域中查找this foo4中的this隐式绑定为person1
// person1.foo4.call(person2)() // person2 箭头函数 向上层作⽤域中查找this foo4中 的this显示绑定为person2
// person1.foo4().call(person2) // person1 箭头函数显示绑定没⽤ 和person1.foo4()()xiang'ton
```

# 作⽤域

## 什么是变量提升、函数提升

变量提升：

简单说就是在js代码执⾏前引擎会先进⾏预编译，预编译期间会将变量声明与函数声明提升⾄其对应作⽤域的最顶端，函数内声明的变量只会提升⾄该函数作⽤域最顶层。当函数内部定义的⼀个变量与外部相同时，那么函数体内的这个变量就会被上升到最顶端。

举个例⼦，如：

```js
console.log(a); // undefined
var a = 3; // 会将var a 的声明提升⾄最顶端
```

函数提升：

函数提升只会提升函数声明式写法，函数表达式的写法不存在函数提升。

函数提升的优先级⼤于变量提升的优先级，即函数提升在变量提升之上

## 说说你对GO/AO/VO的理解

GO

Global Object JS代码在执⾏前会现在堆内存中创建⼀个全局对象(GO)

⽤于存放⼀些定义好的变量⽅法等包含Date Array String Number setTimeout等

同时有⼀个window属性指向⾃⼰

同时在语法分析转成AST的过程中也会将⼀些变量 函数 存放在GO中 只是变量的初始值为undefifined

AO

函数在执⾏前会先在堆内存中创建⼀个AO(Activation Object)对象 ⾥⾯存放这arguments 对应函数的形参 以及在函数中定义的变量 初始值为undefifined

VO

Variable Object 在执⾏函数时 会在执⾏上下⽂栈(ECS)中进⼊⼀个函数执⾏上下⽂(FEC)其中有三个核⼼ 核⼼之⼀是VO 指向的是该函数在内存中解析时创建的AO ⽽在全局执⾏上下⽂中指向的是GO

## 说说你对作⽤域和作⽤域链的理解

作⽤域

在ES5中，全局是⼀个作⽤域，函数也会产⽣作⽤域。在ES6中，代码块、let、const等都会有属于⾃⼰的作⽤域。

作⽤域链

当进⼊到⼀个执⾏上下⽂时，执⾏上下⽂会关联⼀个作⽤域链。通常作⽤域链在解析时就被确定，作⽤域链与函数的定义位置有关，与它的调⽤位置⽆关

## 你是如何理解闭包的,闭包到底是什么

**什么是闭包？**

⼀个普通的函数function，如果它可以访问外层作⽤域的⾃由变量，那么这个函数和周围环境就是⼀个闭包。从狭义的⻆度来说：JavaScript中⼀个函数，如果访问了外层作⽤域的变量，那么它是⼀个闭包

**应⽤场景**

防抖 、节流 、⽴即执⾏函数 、组合函数等等

# 数组

## 常⽤的数组操作⽅法有哪些

Array.shift()

删除并返回第⼀个元素 作⽤：从数组中删除第⼀个元素（即下标为0的元素），并返回该元素。 注意：1）删除元素之后，数组的⻓度-1。

Array.pop()

删除并返回最后⼀个元素 作⽤：从数组中删除最后⼀个元素（即下标为length-1的元素），并返回该元素。 注意：1）删除元素之后，数组的⻓度-1。

Array.push(param1[,param2,...paramN])

尾部添加元素 作⽤：在数组的尾部添加⼀个元素，并返回新数组的⻓度。 注意：1）它是直接修改该数组，⽽不是重新创建⼀个数组。

Array.unshift(newElement1[,newElement2,...newElementN])

头部添加元素 作⽤：在数组的头部添加⼀个或多个元素，并返回新数组的⻓度。 注意：1）它是直接修改该数组，⽽不是重新创建⼀个数组。

Array.join([separator])

转换成字符串 作⽤：把数组的所有元素放⼊到⼀个字符串中。 注意：1）参数separator表示字符串中元素的分隔符，可以为空，默认为半⻆逗号。

Array.reverse()

反转数组 作⽤：把数组的所有元素顺序反转。 注意：1）该⽅法会直接修改数组，⽽不会创建新的数组。

## 数组如何进⾏降维（扁平化）

利⽤Array.some⽅法判断数组中是否还存在数组，es6展开运算符连接数组

```js
let arr = [1,2,[3,4]]
while (arr.some(item => Array.isArray(item))) {
arr = [].concat(...arr);
}
```

使⽤数组的concat⽅法

```js
let arr = [1,2,[3,4]]
let result = []
result = Array.prototype.concat.apply([], arr)
```

使⽤数组的concat⽅法和扩展运算符

```js
var arr = [1,2,[3,4]]
var result = []
result = [].concat(...arr)
```

es6中的flflat函数也可以实现数组的扁平化

```js
let arr = [1,2,['a','b',['中','⽂',[1,2,3,[11,21,31]]]],3];
let result = arr.flat( Infinity )
```

## 数组去重，能⽤⼏种⽅法实现

利⽤ES6 Set去重（ES6中最常⽤）

```js
function unique (arr) {
return Array.from(new Set(arr))
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {}, {}]
```

利⽤for嵌套for，然后splice去重（ES5中最常⽤）

```js
function unique(arr){ 
for(var i=0; i<arr.length; i++){
for(var j=i+1; j<arr.length; j++){
if(arr[i]==arr[j]){ //第⼀个等同于第⼆个，splice⽅法删除第⼆个
arr.splice(j,1);
j--;
 }
 }
 }
return arr; }
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", 15, false, undefined, NaN, NaN, "NaN", "a", {…}, {…}] //NaN和{}没
有去重，两个null直接消失了
```

利⽤indexOf去重

```js
function unique(arr) {
if (!Array.isArray(arr)) {
console.log('type error!')
return
 }
var array = [];
for (var i = 0; i < arr.length; i++) {
if (array .indexOf(arr[i]) === -1) {
array .push(arr[i])
 }
 }
return array; }
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
// [1, "true", true, 15, false, undefined, null, NaN, NaN, "NaN", 0, "a", {…},{…}] 
//NaN、{}没有去重
```

利⽤sort去重

```js
function unique(arr) {
if (!Array.isArray(arr)) {
console.log('type error!')
return;
 }
arr = arr.sort()
var arrry= [arr[0]];
for (var i = 1; i < arr.length; i++) {
if (arr[i] !== arr[i-1]) {
arrry.push(arr[i]);
 }
 }
return arrry; }
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
// [0, 1, 15, "NaN", NaN, NaN, {…}, {…}, "a", false, null, true, "true",undefined] // NaN、{}没有去重
```

利⽤includes

```js
function unique(arr) {
if (!Array.isArray(arr)) {
console.log('type error!')
return
 }
var array =[];
for(var i = 0; i < arr.length; i++) {
if( !array.includes( arr[i]) ) {//includes 检测数组是否有某个值
array.push(arr[i]);
 }
 }
return array
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
// {} 没有去重
```

利⽤filter

```js
function unique(arr) {
return arr.filter(function(item, index, arr) {
//当前元素，在原始数组中的第⼀个索引==当前索引值，否则返回当前元素
return arr.indexOf(item, 0) === index;
 });
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
//[1, "true", true, 15, false, undefined, null, "NaN", 0, "a", {…}, {…}]
```

利⽤递归去重

```js
function unique(arr){
return arr.reduce((prev,cur) => prev.includes(cur) ? prev : [...prev,cur],
[]);
}
var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined,
null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr));
// [1, "true", true, 15, false, undefined, null, NaN, "NaN", 0, "a", {…}, {…}]
```

## 数组中的forEach和map的区别

forEach() 和 map() 两个⽅法都是ECMA5中Array引进的新⽅法，都是⽤来遍历数组中的每⼀项。

它们之间的区别：

1. map速度⽐forEach快
2. map会返回⼀个新数组，不对原数组产⽣影响,foreach不会产⽣新数组，forEach返回undefifined
3. map因为返回数组所以可以链式操作，forEach不能
4. map⾥可以⽤return（return的是什么，相当于把数组中的这⼀项变为什么（并不影响原来的数组，只是相当于把原数组克隆⼀份，把克隆的这⼀份的数组中的对应项改变了） 。
5. forEach⾥⽤return不起作⽤，forEach不能⽤break，会直接报错

## for in和for of的区别

在JavaScript中遍历数组通常是使⽤for...i循环，在ES5具有遍历数组功能的还有forEach、map、fifilter、some、every、reduce、reduceRight等。for...in和for...of是两种增强型循环，for...in是ES5标准，在ES6中新增了for...of的循环⽅式

**for...in** ：遍历以任意顺序迭代⼀个对象的除Symbol以外的可枚举属性，包括继承的可枚举属性。

**for...of**：遍历在可迭代对象，包括 Array ， Map ， Set ， String ， TypedArray ，arguments 对象等等

它们的区别：

1. for...in可以遍历对象和数组，for...of不能遍历对象
2. for...in 循环不仅遍历对象的键名，还会遍历⼿动添加的其它键，甚⾄包括原型链上的键
3. for...in遍历的索引为字符串类型
4. for..of适⽤遍历数/数组对象/字符串/map/set等拥有**迭代器对象**的集合，但是不能遍历对象
5. for...of与forEach()不同的是，它可以正确响应break、continue和return语句具有迭代器对象才可以使⽤for...of

# 函数

## 说出apply、call、bind函数的⽤法和区别

**⽤法：**

apply

第⼀个参数: 绑定this

第⼆个参数: 传⼊额外的实参, 以数组的形式

call

第⼀个参数: 绑定this

参数列表: 后续的参数以多参数的形式传递, 会作为实参bind(不希望obj对象身上有函数)

```js
var bar = foo.bind(obj)
bar() // this -> obj
```

**区别：**

call、apply和bind都可以改变函数的this指向

call、apply和bind第⼀个参数的是this要指向的对象

call、apply和bind都可以后续为函数传参，apply是将参数并成⼀个数组，call和bind是将参数依次列出

call、apply都是直接调⽤，bind⽣成的this指向改变函数需要⼿动调⽤

## 什么是纯函数？如何编写纯函数？

纯函数：纯函数⼀般具有以下的特点：

确定的输⼊⼀定会有确定的输出（外部环境的任何变化不会影响函数内部的操作产⽣的结果）

纯函数的执⾏不会产⽣副作⽤。（函数内部的操作也不会对函数外部产⽣任何影响）

纯函数在react和redux中应⽤⽐较多。

编写纯函数：

```js
//⼀般的数学⽅法可以写成纯函数,例如相加
function sum(...args) {
var result = args.reduce((perValue, item) => {
return preValue + item
 }, 0)
return result
}
```

## 什么是函数柯⾥化？柯⾥化有什么作⽤

函数的柯⾥化：

将传⼊多个参数的函数转变成传⼊单个参数并且返回⼀个函数⽤于接收剩余的参数的函数。

每⼀层函数都接收⼀个参数并对参数进⾏处理。

柯⾥化的作⽤：

单⼀职责：每⼀个函数只⽤处理传⼊的单个参数，每个函数的职责单⼀⽽且确定

参数复⽤：可以拿到每⼀层函数执⾏的返回值作为⼀个新的函数，复⽤已经传⼊过的参数。

组合函数以及组合函数的作⽤？

组合函数：

组合函数是将多个函数组合到⼀起，进⾏依次调⽤的函数使⽤模式。

组合函数的作⽤：

减少重复代码的编写，提⾼代码的复⽤性，便于开发。

可以对任意个函数进⾏组合，返回新的具有多个被组合函数功能的新函数

## 组合函数以及组合函数的作⽤

组合函数：

组合函数是将多个函数组合到⼀起，进⾏依次调⽤的函数使⽤模式。

组合函数的作⽤：

减少重复代码的编写，提⾼代码的复⽤性，便于开发。

可以对任意个函数进⾏组合，返回新的具有多个被组合函数功能的新函数

## 说说你对严格模式的理解

严格模式是⼀种JavaScript的限制模式，因为种种历史原因，JavaScript语⾔在⾮严格模式下是⽐较松散的。在JavaScript不断优化和加⼊新特性的过程中，为了兼容早期的JavaScript，⼀些错误和不规范的写法也被保留了下来。这些错误也不会被抛出。在开启了严格模式后，js引擎会以⼀种更严格的规范执⾏JavaScript代码，⼀些不规范的写法和错误也会直接抛出。

开启严格模式的⽅法：

对⽂件开启：在⽂件的开头写上"use strict"

对函数开启：在函数的开头写上"use strict"

严格模式下的语法限制：

1. 不允许意外创建全局变量（不写var、let、const这种声明变量的关键字）
2. 会对静默失败的赋值操作抛出异常
3. 试图删除不可删除的属性
4. 不允许函数参数有相同的名称
5. 不允许只有0开头的⼋进制语法
6. 不允许使⽤with
7. ⽆法获取eval中定义的变量
8. this绑定不会默认转成对象

# 浏览器和v8引擎

## 浏览器内核是什么？有哪些常⻅的浏览器内核

浏览器内核⼜称浏览器渲染引擎，是浏览器的最核⼼部分。负责解析⽹⻚语法并渲染⽹⻚。

常⻅的浏览器内核有：

trident（三叉戟）---- IE浏览器、360安全浏览器、UC浏览器、搜狗⾼速浏览器、百度浏览器

gecko（壁⻁） ---- Mozilla、Firefox

pestro -> Blink ---- Opera

Webkit ---- Safari、360极速浏览器、搜狗⾼速浏览器、移动端浏览器

Webkit -> Blink ----Chrome、Edge

## 说出浏览器输⼊⼀个URL到⻚⾯显示的过程?

URL 输⼊

​	检查输⼊的内容是否是⼀个合法的 URL 链接

​	判断输⼊的 URL 是否完整, 如果不完整，浏览器可能会对域进⾏猜测，补全前缀或者后缀

​	使⽤⽤户设置的默认搜索引擎来进⾏搜索

DNS 解析

​	浏览器不能直接通过域名找到对应的服务器 IP 地址

​	所以需要进⾏ DNS 解析，查找到对应的 IP 地址进⾏访问。

建⽴ TCP 连接

​	发送 HTTP / HTTPS 请求（建⽴ TLS 连接）

​	向服务器 发起 TCP 连接请求

​	当这个请求到达服务端后，通过 TCP 三次握⼿，建⽴ TCP 的连接。

​		1.客户端发送 SYN 包到服务器，并进⼊ SYN_SEND 状态，等待服务器确认

​		2.服务器收到 SYN 包，必须确认客户的 SYN，同时⾃⼰也发送⼀个 SYN 包，此时服务器进⼊ SYN_RECV 状态。客户端收到服务器的 			SYN包，向服务器发送确认包，此包发送完毕，客户端和服务器进⼊ ESTABLISHED 状态，完成三次握⼿。

服务器响应请求

当浏览器到 web 服务器的连接建⽴后，浏览器会发送⼀个初始的 HTTP GET 请求，请求⽬标通常是⼀个 HTML ⽂件。服务器收到请求后，将发回⼀个 HTTP 响应报⽂，内容包括相关响应头和 HTML 正⽂。

浏览器解析渲染⻚⾯

处理 HTML 标记并构建 DOM 树。

处理 CSS 标记并构建 CSSOM 树。

将 DOM 与 CSSOM 合并成⼀个渲染树根据渲染树来布局，以计算每个节点的⼏何信息。将各个节点绘制到屏幕上。

HTTP 请求结束，断开 TCP 连接.

## 说说你对 JS 引擎的理解

JavaSript 是⼀⻔解释型语⾔

JS 引擎是 JavaScript 语⾔的运⾏解释器

​	浏览器内核中有两种引擎，其中⼀种就是 JS 引擎

​	排版引擎

​		负责 HTML 和 CSS 解析和排版

​	JS 引擎

​		负责解析和运⾏ JavaScript 语句

常⻅ JS 引擎有

1. ​		SpiderMonkey -> 第⼀款 JavaScript 引擎，Brendan Eich 开发
2. ​		Chakra -> 微软开发
3. ​		WebKit -> JavaScriptCore -> APPLE 开发
4. ​		Chrome -> V8 -> GOOGLE 开发
5. ​		⼩程序 -> JSCore -> 腾讯开发

## 说说V8引擎的内存管理

JavaScript的内存管理是⾃动的

关于原始数据类型 直接在栈内存中分配

关于复杂数据类型 在堆内存中分配

## 说说V8引擎的垃圾回收器

因为内存⼤⼩是有限的 所以在内存不需要的时候 需要进⾏释放 ⽤于腾出空间

GC对于内存管理有着对应的算法

常⻅的算法

​	引⽤计数(Reference Count)

​		当⼀个对象有引⽤指向它时 对应的引⽤计数+1

​		当没有对象指向它时 则为0 此时进⾏回收

​		但是有⼀个严重的问题 - 会产⽣循环引⽤

标记清除(Mark-Sweep)

​		核⼼思路: 可达性

​		有⼀个根对象 从该对象出发 开始引⽤到所⽤到的对象 对于根对象没有引⽤到的对象 认为是不可⽤的对象

​		对于不可⽤的对象 则进⾏回收

​		该算法有效的解决了循环引⽤的问题

​		⽬前V8引擎采⽤的就是该算法

V8引擎为了优化 在采⽤标记清除的过程中也引⽤了其他的算法

​		标记整理

​			和标记清除相似 不同的是回收时 会将保留下来的存储对象整合到连续的内存空间 避免

​			内存碎⽚化

​		分代收集(Generational Collection)

​			将内存中的对象分为两组 新的空间 旧的空间

​			对于⻓期存活的对象 会将该对象从新空间移到旧空间中 同时GC检查次数减少

​			将新空间分为from和to 对象的GC查找之后从from移动到to空间中 然后to变为fromfrom变为to 循环⼏次 对于依然存在的对象 移			动到旧空间中

​		增量收集(Increment Collection)

​			如果存在许多对象 则GC试图⼀次性遍历所有的对象 可能会对性能造成⼀定的影响

​			所以引擎试图将垃圾收集⼯作分成⼏部分 然后这⼏部分逐⼀处理 这样会造成微⼩的延迟⽽不是很⼤的延迟

​		闲时收集(IdIe-time Collection)

​			GC只会在CPU空闲的时候运⾏ 减少可能对代码执⾏造成的影响

## v8引擎执⾏代码的⼤致流程

Parse模块：将JavaScript代码转成AST Tree

Ignition :解释器 将ASTTree 转换为字节码(byte Code)同时收集TurboFan 优化需要的信息

TurboFan :编译器 将字节码编译为CPU可以直接执⾏的机器码(machine code).如果某⼀个函数呗被多次调⽤ 则会被标记为热点函数 会经过TurBoFan转换的优化的机器码让CPU执⾏ 提⾼代码性能如果后续执⾏代码过程中 改函数调⽤时的参数类型发⽣了改变 则会逆向的转成字节码 让CPU执⾏

v8引擎执⾏流程:

⾸先会编译JavaScript 编译过程分为三步

1 词法分析(scanner)

​	会将对应的每⼀⾏的代码的字节流分解成有意义的代码块 代码块被称为词法单元(token 进⾏记号化)

2 语法分析(parser)

​	将对应的tokens分析成⼀个元素逐级嵌套的树 这个树称之为 抽象语法树(Abstract SyntaxTree AST)这⾥也有对应的 pre-parser

3 将AST 通过Ignition解释器转换成对应的字节码(ByteCode) 交给CPU执⾏ 同时收集信息

​	将可优化的信息 通过TurBoFan编译器 编译成更好使⽤的机器码交给CPU执⾏.如果后续代码的参数类型发⽣改变 则会逆优化(Deoptimization)为字节码

## 说说线程和进程的区别以及关系

**进程**

​	是 cpu 分配资源的最⼩单位；（是能拥有资源和ᇿ⽴运⾏的最⼩单位）

​	计算机已经运⾏的程序，是操作系统管理程序的⼀种⽅式 (**官⽅说法**)

​	可以认为启动⼀个应⽤程序，就会默认启动⼀个进程（也可能是多个进程）(个⼈解释)

​	也可以说进程是线程的容器

**线程**

​	是 cpu 调度的最⼩单位；（线程是建⽴在进程的基础上的⼀次程序运⾏单位，⼀个进程中可以有多个线程）

​	操作系统能够运⾏运算调度的最⼩单位，通常情况下它被包含在进程中 (官⽅说法)

​	每⼀个进程中，都会启动⾄少⼀个线程⽤来执⾏程序中的代码，这个线程被称之为主线程

**操作系统的⼯作⽅式**

​	如何做到同时让多个进程同时⼯作?

​		因为 CPU 的运算速度⾮常快, 可以快速的在多个进程之间迅速的切换

​		当进程中的线程获取到世间⽚时, 就可以快速执⾏我们编写的代码

​		由于 CPU 执⾏速度过于变态, 对于⽤户来说是感受不到这种快速切换的 

## JavaScript为什么是单线程？

这主要和js的⽤途有关，js是作为浏览器的脚本语⾔，主要是实现⽤户与浏览器的交互，以及操作dom

这决定了它只能是单线程，否则会带来很复杂的同步问题。

⽐如js被设计了多线程，如果有⼀个线程要修改⼀个dom元素，另⼀个线程要删除这个dom元素，此时浏览器就会⼀脸茫然，不知所措。

所以，为了避免复杂性，从⼀诞⽣，JavaScript就是单线程，这已经成了这⻔语⾔的核⼼特征，将来也不会改变

## 浏览器是多进程的?

在浏览器中，每打开⼀个tab⻚⾯，其实就是新开了⼀个进程，在这个进程中，还有ui渲染线程，js引擎线程，http请求线程等。

因此浏览器是⼀个多进程的。为了利⽤多核CPU的计算能⼒，HTML5提出Web Worker标准，允许

JavaScript脚本创建多个线程，但是⼦线程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质

## 什么是重排重绘，如何减少重排重绘

重排(Reflflow)

元素的位置发⽣变动时发⽣重排，也叫回流。此时在关键渲染路径中的 Layout 阶段，计算每⼀个元素在设备视⼝内的确切位置和⼤⼩。当⼀个元素位置发⽣变化时，其⽗元素及其后边的元素位置都可能发⽣变化，代价极⾼

重绘(Repaint)

元素的样式发⽣变动，但是位置没有改变。此时在关键渲染路径中的 Paint 阶段，将渲染树中的每个节点转换成屏幕上的实际像素，这⼀步通常称为绘制或栅格化

另外，重排必定会造成重绘。以下是避免过多重排重绘的⽅法：

1. 使⽤ DocumentFragment 进⾏ DOM 操作，不过现在原⽣操作很少也基本上⽤不到

2. CSS 样式尽量批量修改

3. 避免使⽤ table 布局

4. 为元素提前设置好⾼宽，不因多次渲染改变位置

# ⾯向对象

## 什么是原型、原型链？

**原型：**

在JavaScript中，每⼀个对象都会有⼀个属性[[prototype]]，这个属性就是对象的原型，这个属性的值也是⼀个对象，是原对象的原型对象。访问对象中属性时，会先在对象⾃身进⾏查找，如果没有找到，那么会去对象的原型对象上查找。

**原型链：**

每个对象都有⾃⼰的原型对象，原型对象也有⾃⼰的原型对象。在访问对象的属性时，会沿着对象⾃身=>⾃身的原型对象=>原型对象的原型对象......这样的链条⼀路查找上去，这条链式结构就叫做原型链。原型链的尽头是Object的原型对象的[[prototype]]属性，值为null。

## 如何通过原型链实现继承？

原型链继承：重写⼦类的显式原型对象，让⼦类的显式原型对象的隐式原型指向⽗类的显式原型对象。

```js
function createObject(o) {
  function F() {}
  F.prototype = o
  return new F()
  }
	function inherit(Subtype, Supertype) {
	Subtype.prototype = createObject(Supertype.prototype)
  Object.defineProperty(Subtype.prototype, "constructor", {
  enumerable: false,
  configurable: true,
  writable: true,
  value: Subtype
   })
}
function Person() {}
function Student() {
Person.call(this) }
inherit(Student, Person)
```

## 继承的各个⽅案以及优缺点？

⽅案⼀：直接将⽗类的prototype赋值给⼦类的prototype，⽗类和⼦类共享原型对象

缺点：在⼦类原型对象上添加⽅法和属性会影响到⽗类

```js
function Person() {}
function Student() {}
Student.prototype = Pesrson.prototype
```

⽅案⼆：通过new操作符创建⼀个新的对象，将这个对象作为⼦类的原型对象(显式原型)

缺点：

⼦类的实例对象继承过来的属性是在原型上的，⽆法打印

没有完美的实现属性的继承（⼦类的实对象可以从⽗类继承属性，也可以拥有⾃⼰的属性）

```js
function Person() {}
function Student() {}
var p = new Person()
Student.prototype = p
```

⽅案三：通过new操作符创建⼀个新的对象，将这个对象作为⼦类的原型对象(显式原型)，并且在⼦类的内部通过借⽤构造函数的⽅法实现属性的继承

缺点：⽗类构造函数会被调⽤两次，并且⼦类的实例对象总是有两份相同的属性，⼀份在⾃身，⼀份在其原型对象上

```js
function Person(arg1, arg2) {}
function Student() {
 Person.call(this, arg1, arg2)
}
var p = new Person()
Student.prototype = p
```

⽅案四：让⼦类的原型对象(显式原型)的原型对象(隐式原型)指向⽗类的原型对象(显式原型)

缺点：存在兼容性问题, __proto__ 属性只有部分游览器⽀持

```js
function Person() {}	
function Student() {}
Student.prototype.__proto__ = Person.prototype
```

⽅案五：寄⽣组合式继承(ES5中实现继承的最终⽅案)

```js
function createObject(o) {
 function F() {}
 F.prototype = o
 return new F()
}
function inherit(Subtype, Supertype) {
 Subtype.prototype = createObject(Supertype.prototype)
 Object.defineProperty(Subtype.prototype, "constructor", {
 enumerable: false,
 configurable: true,
 writable: true,
 value: Subtype
 })
}
function Person() {}
function Student() {
 Person.call(this)
}
inherit(Student, Person)
```

## 说说你对⾯向对象多态的理解

当对不同的数据类型执⾏同⼀个操作时, 如果表现出来的⾏为(形态)不⼀样, 那么就是多态的体现

继承也是多态的前提

# ES6-13

## 说说let、const和var的区别？

作⽤域提升

​	var声明的变量是会进⾏作⽤域提升

​	let、const没有进⾏作⽤域提升，但是会在解析阶段被创建出来

​	let,const具有暂时性死区

块级作⽤域

​	var不存在块级作⽤域

​	let和const存在块级作⽤域

重复声明

​	var允许重复声明变量

​	let和const在同⼀作⽤域不允许重复声明变量

修改声明的变量

​	let,var 可以修改声明的变量

​	const它表示保存的数据⼀旦被赋值，就不能被修改,但是如果赋值的是引⽤类型，那么可以通过引⽤找到对应的对象，修改对象的内容

## 说说ES6~ES13新增了哪些知识点

ES6 :

​	使⽤class⽤来定义类

​		constructor构造器

​		extends实现继承

​		super关键字代表继承的⽗类

​	对象字⾯量的增强

​		属性的简写

​		⽅法的简写

​		计算属性名

​	解构

​	let/const的使⽤

​		不能重复声明变量

​		不存在作⽤域提升

​		存在暂时性死区

​		不添加window

​		存在块级作⽤域

字符串模板

​	在模板字符串中，我们可以通过 ${expression} 来嵌⼊动态的内容

​	标签模板字符串

函数的默认参数

函数的剩余参数

箭头函数

​	没有显式原型prototype

​	不绑定this、arguments、super参数

展开语法

​	在函数调⽤时使⽤；

​	在数组构造时使⽤；

​	展开运算符其实是⼀种浅拷⻉

​	在构建对象字⾯量时，也可以使⽤展开运算符，这个是在ES2018（ES9）中添加的新特性；

规范了⼆进制和⼋进制的写法

新增Symbol

Set、WeakSet、Map、WeakMap

ES7 :

​	Array Includes

​		通过includes来判断⼀个数组中是否包含⼀个指定的元素，根据情况，包含返回 true，否则返回false。

​	指数exponentiation运算符

​	**对数字来计算乘⽅。

ES8 :

​	Object values

​		通过Object.values 来获取所有的value值

​	Object entries

​		通过 Object.entries 可以获取到⼀个数组，数组中会存放可枚举属性的键值对数组

​	String Padding

​		padStart 和 padEnd ⽅法，分别对字符串的⾸尾进⾏填充的。

​	Trailing Commas

​		允许在函数定义和调⽤时多加⼀个逗号：

​	Object.getOwnPropertyDescriptors

ES9 :

​	构建对象字⾯量时，可以使⽤展开运算符

ES10 :

​	flat

​		flat() ⽅法会按照⼀个可指定的深度递归遍历数组，并将所有元素与遍历到的⼦数组中的元素合并为⼀个新数组返回。

​	flatMap

​		flatMap是先进⾏map操作，再做flat的操作

​	flatMap中的flat相当于深度为1

​	Object fromEntries

​		Object.formEntries将entries转换成⼀个对象

​	trimStart trimEnd

​		去除字符串前⾯或者后⾯的空格

ES11 :

​	BigInt

​		BigInt，⽤于表示⼤的整数(超过最⼤安全整数)

​		BitInt的表示⽅法是在数值的后⾯加上n

​	空值合并操作符

​		??当前⾯的值为null或者undefifined是,显式??后⾯的值

​	Optional Chaining

​		可选链?.

​		当?.前⾯的值为空时返回undefifined

Global ThisJavaScript环境的全局对象

for..in标准化

for...in遍历对象时遍历的是key

## 说说Set、WeakSet、Map、WeakMap的特点

**Set:**

⽤来存储数据,类似于数组,

与数组的区别是元素不能重复,

可以使⽤forEach⽅法和使⽤for...of...遍历

常⻅属性和⽅法

​	size：返回Set中元素的个数

​	add(value)：添加某个元素，返回Set对象本身

​	delete(value)：从set中删除和这个值相等的元素，返回boolean类型

​	has(value)：判断set中是否存在某个元素，返回boolean类型

​	clear()：清空set中所有的元素

​	forEach(callback, [, thisArg])：通过forEach遍历set

**WeakSet:**

只能存储对象类型,不能存放基本数据类型,

对对象的引⽤是⼀个弱引⽤,如果没有其他对对象的引⽤,那么相应对象会被GC进⾏清除,

不能遍历

常⻅的⽅法

​	add(value)：添加某个元素，返回WeakSet对象本身

​	delete(value)：从WeakSet中删除和这个值相等的元素，返回boolean类型

​	has(value)：判断WeakSet中是否存在某个元素，返回boolean类型

**Map:**

⽤于存储映射关系,存储的为键值对,

每个键值对为⼀个数组,

与对象的区别是存储的key可以为⼀个对象

可以使⽤forEach⽅法和使⽤for...of...遍历

常⻅属性和⽅法

​	size：返回Set中元素的个数

​	set(key, value)：在Map中添加key、value，并且返回整个Map对象

​	get(key)：根据key获取Map中的value

​	has(key)：判断是否包括某⼀个key，返回Boolean类型

​	delete(key)：根据key删除⼀个键值对，返回Boolean类型

​	clear()：清空所有的元素

​	forEach(callback, [, thisArg])：通过forEach遍历Map

**WeakMap:**存储的key只能为对象,不允许是其他类型

对对象的引⽤是⼀个弱引⽤,如果没有其他对对象的引⽤,那么相应对象会被GC进⾏清除,

不能进⾏遍历

常⻅的⽅法

​	set(key, value)：在Map中添加key、value，并且返回整个Map对象

​	get(key)：根据key获取Map中的value

​	has(key)：判断是否包括某⼀个key，返回Boolean类型

​	delete(key)：根据key删除⼀个键值对，返回Boolean类型