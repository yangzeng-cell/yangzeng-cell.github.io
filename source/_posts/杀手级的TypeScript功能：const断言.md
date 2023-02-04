---
title: 杀手级的TypeScript功能：const断言
date: 2022-10-06 18:00:50
tags:
- [typescript]
categories:
- [typescript, note]
---

###

`const assertions`是ts3.4新增的功能，们可以用这个新功能省略很多繁琐的类型声明

### const assertions

```js
const x = { text: "hello" } as const;
```

官方的解释是

const断言是ts3.4新增的一种构造函数，他的语法是用const代替type的类型断言，例如 `123 as const`），当我们使用const进行断言literal expression的时候，会做以下处理

1. 该表达式的字面量类型不会被扩展
2. 对象字面量获取的是readonly属性
3. array literals将会变成readonly tuples

官方的解释会比较困惑

### No type widening for literal types [文字类型没有类型扩展]

通过const断言，文字类型不会被扩展为更加宽泛的类型，当我们使用const声明字面量变量的时候，类型就会等于右侧的字面量

```js
const x = 'x'; // x has the type 'x'
```

const关键字可以确保不会重新分配变量类型并且会保证更加严格的类型定义，但是，如果我们使用let替代const的时候，let就会变成更加宽泛的类型

```js
const x = 'x'; // has the type 'x' 
let y = 'x';   // has the type string
```

y会扩展为更加宽泛的值，允许分配其他值给y,而x只能是字面量的x,如果我们向让let也可以这么做，就可以使用const断言

```
let y = 'x' as const; // y has type 'x'
```

### object literals get readonly properties [对象字面量获得只读属性]

```js
const action = { type: 'INCREMENT', } // has type { type: string }
```

const声明对象时，却依然会出现属性的扩展

如果你熟悉使用redux的时候，action用于执行修改变量，对外界来说，redux是全局不可修改的state store,state只能通过action在reducer中进行修改，reducer是纯函数，他会返回更新之后的state的版本，在action被dispatch之后、

在redux中，通常情况下，是在actionCreator中创建action,actionCreators是纯函数，它会返回redux action的字面量的对象，以及提供参数给action

用一个例子进行解释说明

```js
const setCount = (n: number) => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const action = setCount(3)
// action has type
// { type: string, payload: number }
```

type的类型应该是字面量的，但是会被自动转换成更加宽泛的类型，在TS3.4以前，我们需要显示的指定返回值类型才可以达到类似的效果，

```tsx
interface SetCount {
  type: 'SET_COUNT';
  payload: number;
}

const setCount = (n: number): SetCount => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const action = setCount(3)
// action has type SetCount
```

ts3.4以后使用const断言也可以实现同样的效果,而且是只读属性的

```tsx
const setCount = (n: number) => {
  return <const>{
    type: 'SET_COUNT',
    payload: n
  }
}

const action = setCount(3);
// action has type
//  { readonly type: "SET_COUNT"; readonly payload: number };
```

对于使用了联合类型的action,在没有使用const断言的时候会比较麻烦

```tsx
interface SetCount {
  type: 'SET_COUNT';
  payload: number;
}

interface ResetCount {
  type: 'RESET_COUNT';
}

const setCount = (n: number): SetCount => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const resetCount = (): ResetCount => {
  return {
    type: 'RESET_COUNT',
  }
}

type CountActions = SetCount | ResetCount
```

通过使用const的用法

```tsx
const setCount = (n: number) => {
  return <const>{
    type: 'SET_COUNT',
    payload: n
  }
}

const resetCount = () => {
  return <const>{
    type: 'RESET_COUNT'
  }
}

type CountActions = ReturnType<typeof setCount> | ReturnType<typeof resetCount>;
```

### Array literals become `readonly` tuples

```
const action = {
  type: 'SET_HOURS',
  payload: [8, 12, 5, 8],
}
//  { type: string; payload: number[]; }

action.payload.push(12) // no error
```

action。payload是可以修改的

如果使用了const断言

```js
const action = <const>{
  type: 'SET_HOURS',
  payload: [8, 12, 5, 8]
}

// {
//  readonly type: "SET_HOURS";
//  readonly payload: readonly [8, 12, 5, 8];
// }

action.payload.push(12);  // error - Property 'push' does not exist on type 'readonly [8, 12, 5, 8]'.
```

#### 总结

```js
let obj = {
  x: 10,
  y: [20, 30],
  z: {
    a:
      {  b: 42 }
  } 
} as const;
```

转换成

```js
let obj: {
  readonly x: 10;
  readonly y: readonly [20, 30];
  readonly z: {
    readonly a: {
      readonly b: 42;
    };
  };
};
```
