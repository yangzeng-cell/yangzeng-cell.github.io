---
title: Apply函数的实现
date: 2022-09-07 15:51:49
tags:
categories:
- [javascript, 原理]
---

```js
Function.prototype.apply = function (thisArg, args) {
  const fn = this;
  thisArg = thisArg != null ? Object(thisArg) : globalThis;

  if (!(args instanceof Array)) {
    throw new Error("args is no array");
  }
  args = args || [];

  thisArg.fn = fn;

  const result = thisArg.fn(...args);

  delete thisArg.fn;

  return result;
};

```

