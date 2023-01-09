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