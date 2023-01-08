---
title: 前端八股文-JS基础
date: 2022-09-04 10:46:08
tags:
- [javascript]
categories:
- [前端八股文]
---

# 02-JS基础常⻅⾯试题

## 说出 JavaScript 中的常⻅数据类型

Number -> 代表数据类型是数值

整数

浮点

数组

String -> 代表字符串类型 -> 通常是⼀段⽂本

boolean -> 布尔类型

true

false

NULL -> 空值

undefifined -> 变量未定义

Object -> 对象类型

BigInt -> ⼤整数类型

Symbol -> 符号类型

## 说出count++和++count的区别?

如果它们在计算之后的值不会被使⽤ 是没有区别的

如果需要⽴刻使⽤⾃增之后的值 就⽤前置型 反之后置型

```js
var num = 1 + ++count // 不推荐这么写
//需要⽴刻使⽤的话
count++
var num = 1 +count //++count
//不需要的话
var num = 1 + count //count++
count++
```

## 说出==和===的区别?

1. == (普通相等)

在类型不相同的情况下, 会将运算元先转成Number的值, 再进⾏⽐较(隐式转换)

null⽐较特殊: null在进⾏⽐较的时候, 应该是会被当成⼀个对象和原⽣类型进⾏⽐较的

 2.=== (严格不等)

在类型不同的情况下,直接返回false

## 逻辑与**&&和逻辑或||的本质和区别？**