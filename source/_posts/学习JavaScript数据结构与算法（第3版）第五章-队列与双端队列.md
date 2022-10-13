---
title: 学习JavaScript数据结构与算法（第3版）第五章 队列与双端队列
date: 2022-09-17 00:07:31
tags:
categories:
- [数据结构]
---

#### ##队列和双端队列



```js
class Queue {
  /**
   * 首先需要一个用于存储队列中元素的数据结构。我们可以使用数组，就像上一章的 Stack
类那样。但是，为了写出一个在获取元素时更高效的数据结构，我们将使用一个对象来存储我们
的元素（行{3}）。你会发现 Queue 类和 Stack 类非常类似，只是添加和移除元素的原则不同
也可以声明一个 count 属性来帮助我们控制队列的大小（行{1}）。此外，由于我们将要从队
列前端移除元素，同样需要一个变量来帮助我们追踪第一个元素。因此，声明一个 lowestCount
变量（行{2}）
   */
  constructor() {
    this.count = 0;
    this.lowestCount = 0;
    this.items = {};
  }
  // 向队列尾部添加一个（或多个）新的项
  enqueue(...elements) {
    for (let i = 0; i < elements.length; i++) {
      this.items[this.count] = elements[i];
      this.count++;
    }
  }

  // 移除队列的第一项（即排在队列最前面的项）并返回被移除的元素
  dequeue() {
    if (this.count === 0) {
      return undefined;
    }

    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;

    return result;
  }
  // 返回队列中第一个元素——最先被添加，也将是最先被移除的元素。队列不做
  // 任何变动（不移除元素，只返回元素信息——与 Stack 类的 peek 方法非常类似）。该方
  // 法在其他语言中也可以叫作 front 方法
  peek() {
    if (this.count === 0) {
      return undefined;
    }

    return this.items[this.lowestCount];
  }
  // 如果队列中不包含任何元素，返回 true，否则返回 false
  isEmpty() {
    return this.count - this.lowestCount === 0 ? true : false;
  }
  // 返回队列包含的元素个数，与数组的 length 属性类似
  size() {
    return this.count - this.lowestCount;
  }

  clear() {
    this.count = 0;
    this.lowestCount = 0;
    this.items = {};
  }

  toString() {
    let str = "";
    if (this.isEmpty()) {
      return str;
    }
    str = this.items[this.lowestCount];
    for (let i = this.lowestCount + 1; i < this.count; i++) {
      str += `,${this.items[i]}`;
    }

    return str;
  }
}

// const queue = new Queue();
// console.log(queue.isEmpty()); // 输出 true

// queue.enqueue("John");
// queue.enqueue("Jack");
// console.log(queue.toString()); // John,Jack
// queue.enqueue("Camila");
// console.log(queue.toString()); // John, Jack, Camila
// console.log(queue.size()); // 输出 3
// console.log(queue.isEmpty()); // 输出 false
// queue.dequeue(); // 移除 John
// queue.dequeue(); // 移除 Jack
// console.log(queue.toString()); // Camila

export default Queue;

```

#### 双端队列数据结构

```js
class Deque {
  // 双端队列，遵循先进先出和后进先出

  constructor() {
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }

  isEmpty() {
    return this.count - this.lowestCount === 0;
  }

  clear() {
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }

  size() {
    return this.count - this.lowestCount;
  }

  toString() {
    let str = "";

    if (this.isEmpty()) {
      return str;
    }

    str = this.items[this.lowestCount];

    for (let i = this.lowestCount + 1; i < this.count; i++) {
      str += `,${this.items[i]}`;
    }

    return str;
  }
  // 该方法在双端队列前端添加新的元素
  addFront(element) {
    if (this.isEmpty()) {
      this.addBack(element);
    } else if (this.lowestCount > 0) {
      this.lowestCount--;
      this.items[this.lowestCount] = element;
    } else {
      for (let i = this.count; i > 0; i--) {
        this.items[i] = this.items[i - 1];
      }
      this.items[0] = element;
      this.lowestCount = 0;
      this.count++;
    }
  }

  // 该方法在双端队列后端添加新的元素（实现方法和 Queue 类中的enqueue 方法相同）
  addBack(element) {
    this.items[this.count] = element;
    this.count++;
  }
  // 该方法会从双端队列前端移除第一个元素（实现方法和 Queue 类中的dequeue 方法相同）
  removeFront() {
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;

    return result;
  }

  //该方法会从双端队列后端移除第一个元素（实现方法和 Stack 类中的pop 方法一样）。
  removeBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;

    const result = this.items[this.count];

    delete this.items[this.count];

    return result;
  }

  // 该方法返回双端队列前端的第一个元素（实现方法和 Queue 类中的 peek方法一样）。
  peekFront() {
    if (this.isEmpty()) {
      return undefined;
    }

    return this.items[this.lowestCount];
  }

  // 该方法返回双端队列后端的第一个元素（实现方法和 Stack 类中的 peek方法一样）
  peekBack() {
    if (this.isEmpty()) {
      return undefined;
    }

    return this.items[this.count];
  }
}

// const deque = new Deque();
// console.log(deque.isEmpty()); // 输出 true
// deque.addBack("John");
// deque.addBack("Jack");
// console.log(deque.toString()); // John, Jack
// deque.addBack("Camila");
// console.log(deque.toString()); // John, Jack, Camila
// console.log(deque.size()); // 输出 3
// console.log(deque.isEmpty()); // 输出 false
// deque.removeFront(); // 移除 John
// console.log(deque.toString()); // Jack, Camila
// deque.removeBack(); // Camila 决定离开
// console.log(deque.toString()); // Jack
// deque.addFront("John"); // John 回来询问一些信息
// console.log(deque.toString()); // John, Jack

export default Deque;

```

#### 使用队列和双端队列来解决问题

##### 循环队列——击鼓传花游戏

这其中的一种叫作循环队列。循环队列的一个例子就是击鼓传花游戏（hot  potato）。在这个游戏中，孩子们围成一个圆圈，把花尽快地传递给旁边的人。某一时刻传花停止， 这个时候花在谁手里，谁就退出圆圈、结束游戏。重复这个过程，直到只剩一个孩子（胜者）。

实现一个模拟的击鼓传花游戏，要用到本章开头实现的 Queue 类（行{1}）。我们会得到一 份名单，把里面的名字全都加入队列（行{2}）。给定一个数字，然后迭代队列。从队列开头移 除一项，再将其添加到队列末尾（行{3}），模拟击鼓传花（如果你把花传给了旁边的人，你被 淘汰的威胁就立刻解除了）。一旦达到给定的传递次数，拿着花的那个人就被淘汰了（从队列中 移除——行{4}）。最后只剩下一个人的时候，这个人就是胜者（行{5}）

```js
import Queue from "./Queue.js";

function hotPotato(elementsList, num) {
  const queue = new Queue();

  const elimitatedList = [];

  for (let i = 0; i < elementsList.length; i++) {
    queue.enqueue(elementsList[i]);
  }
  console.log(queue.toString(), "----");
  while (queue.size() > 1) {
    for (let i = 0; i < num; i++) {
      queue.enqueue(queue.dequeue());
    }
    elimitatedList.push(queue.dequeue());
  }

  return {
    winner: queue.dequeue(),
    eliminated: elimitatedList,
  };
}

const names = ["John", "Jack", "Camila", "Ingrid", "Carl"];
const result = hotPotato(names, 7);

result.eliminated.forEach((name) => {
  console.log(`${name}在击鼓传花游戏中被淘汰。`);
});
console.log(`胜利者： ${result.winner}`);

```

以上算法的输出如下。

 Camila 在击鼓传花游戏中被淘汰。

 Jack 在击鼓传花游戏中被淘汰。

 Carl 在击鼓传花游戏中被淘汰。

 Ingrid 在击鼓传花游戏中被淘汰。 

 胜利者：John

##### 回文检查器

下面是维基百科对回文的解释。 回文是正反都能读通的单词、词组、数或一系列字符的序列，例如 madam或 racecar。 有不同的算法可以检查一个词组或字符串是否为回文。最简单的方式是将字符串反向排列并 检查它和原字符串是否相同。如果两者相同，那么它就是一个回文。我们也可以用栈来完成，但 是利用数据结构来解决这个问题的最简单方法是使用双端队列

```js
import Deque from "./Deque.js";
// 回文字符串检测
function palindromeChecker(aString) {
  if (aString == null || (aString != null && aString.length === 0)) {
    return false;
  }
  // 判断是否相等，不相等就返回
  let isEqual = true;
  // 获得第一个和最后一个字符
  let firstChar, lastChar;
  const deque = new Deque();
  // 先将字符串转成小写然后再去掉空格重新拼接
  aString = aString.toLocaleLowerCase().split(" ").join("");
  console.log(aString);
  for (let i = 0; i < aString.length; i++) {
    deque.addBack(aString.charAt(i));
  }
  // while循环拿到第一个和最后一个进行比较，只有在isEqual为true才继续比奥,只有一个的时候跳出
  while (deque.size() > 1 && isEqual) {
    firstChar = deque.removeFront();
    lastChar = deque.removeBack();

    if (lastChar !== firstChar) {
      isEqual = false;
    }
  }

  return isEqual;
}
// 全是true
console.log("a", palindromeChecker("a"));
console.log("aa", palindromeChecker("aa"));
console.log("kayak", palindromeChecker("kayak"));
console.log("level", palindromeChecker("level"));
console.log(
  "Was it a car or a cat I saw",
  palindromeChecker("Was it a car or a cat I saw")
);
console.log("Step on no pets", palindromeChecker("Step on no pets"));

```

在我们开始解释算法逻辑之前，需要检查传入的字符串参数是否合法（行{1}）。如果不合 法，我们返回 false。 对于这个算法，我们将使用在本章实现的 Deque 类（行{2}）。由于可能接收到同时包含大 小写字母的字符串，我们会将所有字母转化为小写，同时移除所有的空格（行{3}）。如果你愿意， 也可以移除所有的特殊字符，例如!、?、-、(和)等。为了保证算法简洁，我们会跳过这部分。 然后，我们会对字符串中的所有字符执行 enqueue 操作（行{4}）。如果所有元素都在双端 队列中（如果只有一个字符的话，那它肯定是回文）并且首尾字符相同的话（行{5}），我们将 从前端移除一个元素（行{6}），再从后端移除一个元素（行{7}）。要使字符串为回文，移除的 两个字符必须相同。如果字符不同的话，这个字符串就不是一个回文（行{8}）
