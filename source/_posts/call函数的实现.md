---
title: call函数的实现
date: 2022-09-07 15:52:13
tags:
categories:
- [javascript, 原理]
---

```js
Function.prototype.MyCall = function (thisArg, ...args) {
  const fn = this;
  thisArg = thisArg != null ? Object(thisArg) : globalThis; //Object(thisArg)如果是基本类型将会将换成包装类，globalThis分别对应window/node的环境
  args = args || [];
  thisArg.fn = fn;
  const result = thisArg.fn(...args);

  delete thisArg.fn;

  return result;
};

function foo() {
  console.log("foo函数被执行", this);
}

function sum(num1, num2) {
  console.log("sum函数被执行", this, num1, num2);
  return num1 + num2;
}

foo.call(undefined);
var result = sum.call({}, 20, 30);
// console.log("系统调用的结果:", result)
// 自己实现的函数的hycall方法
// 默认进行隐式绑定
// foo.hycall({name: "why"})
foo.MyCall(undefined);
var result = sum.MyCall("abc", 20, 30);
console.log("hycall的调用:", result);

```

