---
title: 实现bind
date: 2022-09-05 09:37:41
tags:
---

```js
Function.prototype.mybind = function (thisArg, ...args) {
  // 获得this的指向，就是实际的调用者
  let fn = this;
  // 获取thisArg,如果是基本数据类型除了null,undefined都转成包装类型，否则指向全局
  thisArg = thisArg != null ? Object(thisArg) : globalThis;
  // bind会返回一个新的函数
  return function newFn(...argArr) {
    // 向thisArg对象添加这个方法
    thisArg.fn = fn;
    // 合并参数
    const argTotals = [...args, ...argArr];
    // 执行函数
    const result = thisArg.fn(...argTotals);
    // 执行完成后删除方法
    delete thisArg.fn;

    return result;
  };
};

function foo() {
  console.log("foo被执行", this);
  return 20;
}

function sum(num1, num2, num3, num4) {
  console.log(num1, num2, num3, num4);
}

// 系统的bind使用
var bar = foo.bind("abc");
bar();

var newSum = sum.bind("aaa", 10, 20, 30, 40);
newSum();

// 使用自己定义的bind
var bar = foo.mybind("abc");
var result = bar();
console.log(result);

var newSum = sum.mybind("abc", 10, 20);
var result = newSum(30, 40);
```

