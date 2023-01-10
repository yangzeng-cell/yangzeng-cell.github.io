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