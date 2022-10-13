---
title: 学习JavaScript数据结构与算法（第3版）读书笔记
date: 2022-09-14 13:50:51
tags:
categories:
- [数据结构]
---

#### ##第四章栈

使用数组来定义数据结构

```js
class Stack {
  constructor() {
    this.items = [];
  }
  // 添加一个（或几个）新元素到栈顶,返回值是数组的长度
  push(element) {
    return this.items.push(element);
  }
  //移除栈顶的元素，同时返回被移除的元素
  pop() {
    return this.items.pop();
  }

  //返回栈顶的元素，不对栈做任何修改（该方法不会移除栈顶的元素，仅仅返回它）
  peek() {
    return this.items[this.items.length - 1];
  }

  // 如果栈里没有任何元素就返回 true，否则返回 false
  isEmpty() {
    return this.items.length > 0 ? false : true;
  }

  // 移除栈里的所有元素
  clear() {
    this.items = [];
  }

  //返回栈里的元素个数。该方法和数组的 length 属性很类似
  size() {
    return this.items.length;
  }
}

```

使用对象来定义栈

```js
class Stack {
  constructor() {
    this.count = 0;
    this.items = {};
  }
  push(element) {
    this.items[this.count] = element;
    this.count++;
  }
  size() {
    return this.count;
  }
  isEmpty() {
    return this.count === 0;
  }
  pop() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;  }
  clear() {
    this.count = 0;
    this.items = {};
    // while(!this.isEmpty()){
    //   this.pop()
    // }
  }
  peek() {
    if (!this.isEmpty()) {
      return undefined;
    }
    return this.items[this.count - 1];
  }
  toString() {
    if (!this.isEmpty()) {
      return "";
    }
    let str = this.items[0];
    for (let i = 1; i < this.count; i++) {
      str = str + `,${this.items[i]}`;
    }
    return str;
  }
}
```

保护数据结构内部元素

```js
const stack = new Stack();
console.log(Object.getOwnPropertyNames(stack)); // {1}
console.log(Object.keys(stack)); // {2}
console.log(stack.items);
```

外部可以直接拿到内部的数据，因为js中没有私有属性

```js
class Stack { 
 constructor() { 
 this._count = 0; 
 this._items = {}; 
 } 
}
```

一部分开发者胡使用下划线作为私有属性的规范，但是这样依然可以被访问，没有实质性的作用

##### 用 ES2015 的限定作用域 Symbol 实现类

```js
const _items = new Symbol("stackItem");

class Stack {
  constructor() {
    //使用计算属性和symbol的形式
    this[_items] = {};
  }
}
```

虽然这种方式可以让上述方法没办法访问内部属性但是可以通过`Object.getOwnPropertySymbol(stack)`可以访问到以Symbol为key的属性



##### 用 ES2015 的 WeakMap 实现类

```js
const items=new WeakMap()
class WeakMapStack{
  constructor(){
    items.set(this,[])
  }

  push(element){
    items.get(this).push(element)
  }

  pop(){
    return items.get(this).pop()
  }

  //...
}
```

现在我们知道了，items 在 Stack 类里是真正的私有属性。采用这种方法，代码的可读性 不强，而且在扩展该类时无法继承私有属性。鱼和熊掌不可兼得！

##### 使用栈的实例



```js
// 将十进制转成二进制
import Stack from "./Stack.js";
function decimalToBinary(decNumber) {
  decNumber = Math.floor(Number(decNumber));
  const stack = new Stack();

  let dec = decNumber;

  while (dec > 0) {
    console.log(Math.floor(dec % 2));
    stack.push(dec % 2);
    dec = Math.floor(dec / 2);
  }
  let binaryString = "";
  while (!stack.isEmpty()) {
    binaryString += stack.pop().toString();
  }
  return binaryString;
}

```

```js
// 把十进制转换成基数为 2～36 的任意进制
import Stack from "./Stack.js";

function baseConverter(decNumber, base) {
  let dec = decNumber;
  let arr = new Stack();
  const digits = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
  let str = "";
  if (!(base >= 2 && base <= 36)) {
    return str;
  }
  while (dec > 0) {
    arr.push(Math.floor(dec % base));
    dec = Math.floor(dec / base);
  }

  while (!arr.isEmpty()) {
    str += digits[arr.pop()];
  }

  return str;
}

console.log(baseConverter(100345, 2)); // 11000011111111001
console.log(baseConverter(100345, 8)); // 303771
console.log(baseConverter(100345, 16)); // 187F9
console.log(baseConverter(100345, 35)); // 2BW0
```

