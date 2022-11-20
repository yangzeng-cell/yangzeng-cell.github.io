---
title: 学习JavaScript数据结构与算法（第3版）第六章链表
date: 2022-09-17 13:46:40
tags:
- [数据结构]
categories:
- [数据结构]
---

# 链表

## Util.js

```js
// 对两个数值进行浅层比较

export function defaultEquals(a, b) {
  return a === b;
}

export function defaultToString(item) {
  if (item === null) {
    return "NULL";
  } else if (item === undefined) {
    return "undefined";
  } else if (typeof item === "string" || item instanceof String) {
    return `${item}`;
  }

  return item.toString();
}

export const Compare = {
  LESS_THAN: -1,
  BIGGER_THAN: 1,
};

export function defaultCompare(a, b) {
  if (a === b) {
    return 0;
  }
  return a < b ? Compare.LESS_THAN : Compare.BIGGER_THAN;
}

```

## linked-list-models.js

```js
// 定义每个节点的信息
export class Node {
  constructor(element) {
    this.element = element;
    this.next = undefined;
  }
}

export class DoublyNode extends Node {
  constructor(element, next, prev) {
    super(element, next);
    this.prev = prev;
  }
}

export class ValuePair {
  constructor(key, value) {
    this.key = key;
    this.value = value;
  }

  toString() {
    return `[${this.key},${this.value}]`;
  }
}

```



## 普通链表

```js
import { defaultEquals } from "../util.js";
import { Node } from "../linked-list-models.js";

export default class LinkedList {
  constructor(equalFn = defaultEquals) {
    // 用来存储链表中的元素数量
    this.count = 0;
    this.head = undefined;
    //自定义 用来比较是否相等 如果没有定义则使用默认的比较方法
    this.equalFn = equalFn;
  }
  //向链表尾部添加一个新元素
  push(element) {
    const node = new Node(element);
    // 先判断链表为空的情况
    if (this.head === undefined) {
      this.head = node;
    } else {
      let current = this.head;
      // 循环找到最后一个节点
      while (current.next != null) {
        current = current.next;
      }

      current.next = node;
    }
    // 数值加一
    this.count++;
  }

  // 向链表的特定位置插入一个新元素
  insert(element, position) {
    if (position >= 0 && position <= this.count) {
      const node = new Node(element);
      if (position === 0) {
        const current = this.head;
        this.head = node;
        node.next = current;
      } else {
        const previous = this.getElementAt(position - 1);
        const current = previous.next;
        previous.next = node;
        node.next = current;
      }
      this.count++;
      return true;
    }
    return false;
  }

  // 返回链表中特定位置的元素。如果链表中不存在这样的元素，则返回 undefined
  getElementAt(index) {
    if (index < 0 || index >= this.count) {
      return undefined;
    }

    let current = this.head;

    for (let i = 0; i < index && current != null; i++) {
      current = current.next;
    }

    return current;
  }

  // 从链表中移除一个元素
  remove(element) {
    const currentIndex = this.indexOf(element);
    return this.removeAt(currentIndex);
  }

  // 返回元素在链表中的索引。如果链表中没有该元素则返回-1
  indexOf(element) {
    let current = this.head;
    for (let i = 0; i < this.count && current != null; i++) {
      if (this.equalFn(element, current.element)) {
        return i;
      }
      current = current.next;
    }
    return -1;
  }

  // 从链表的特定位置移除一个元素
  removeAt(position) {
    // 边界判断
    if (position >= this.count || position < 0) {
      return undefined;
    }

    let current = this.head;
    let previous = this.head;
    // 当是第一个值时
    if (position === 0) {
      this.head = current.next;
    } else {
      // for循环拿到curent
      // for (let i = 0; i < position; i++) {
      //   /**
      //    * 获得之前的值，当到达目标值的时候，current.next就指向下一个值了，previous指向前一个之
      //    */
      //   previous = current;
      //   current = current.next;
      // }
      // 重构
      previous = this.getElementAt(position - 1);
      current = previous.next;
      previous.next = current.next;
    }
    this.count--;

    return current.element;
  }

  // 如果链表中不包含任何元素，返回 true，如果链表长度大于 0则返回 false
  isEmpty() {
    return this.count === 0;
  }

  // 返回链表包含的元素个数，与数组的 length 属性类似
  size() {
    return this.count;
  }

  // 返回表示整个链表的字符串。由于列表项使用了 Node 类，就需要重写继承自 JavaScript 对象默认的 toString 方法，让其只输出元素的值
  toString() {
    if (this.head === null) {
      return "";
    }
    let objString = `${this.head.element}`;
    let current = this.head.next;
    for (let i = 1; i < this.count && current != null; i++) {
      objString += `,${current.element}`;
      current = current.next;
    }

    return objString;
  }
}

```

## 双向链表

```js
import { DoublyNode } from "../linked-list-models.js";
import { defaultEquals } from "../util.js";
import LinkedList from "./LinkedList.js";

export default class DoublyLinkedList extends LinkedList {
  constructor(equalsFn = defaultEquals) {
    super(equalsFn);
    this.tail = undefined;
  }
  insert(element, index) {
    if (index >= 0 && index <= this.count) {
      const node = new DoublyNode(element);
      let current = this.head;
      if (index === 0) {
        if (thid.head === null) {
          this.head = node;
          this.tail = node;
        } else {
          node.next = this.head;
          current.prev = node;
          this.head = node;
        }
      } else if (index === this.count) {
        current = this.tail;
        current.next = node;
        node.prev = current;
        this.tail = node;
      } else {
        const previous = this.getElementAt(index - 1);
        current = previous.next;
        previous.next = node;
        node.prev = previous;
        node.next = current;
        current.prev = node;
      }
      this.count++;
      return true;
    }

    return falses;
  }

  removeAt(index) {
    if (index >= 0 && index < this.count) {
      let current = this.head;
      if (index === 0) {
        this.head = current.next;
        if (this.count === 1) {
          this.tail = undefined;
        } else {
          current.prev = undefined;
        }
      } else if ((index = this.count - 1)) {
        current = this.getElementAt(index);
        this.tail = current.prev;
        this.tail.next = undefined;
      } else {
        current = this.getElementAt(index);
        current.prev.next = current.next;
        current.next.prev = current.prev;
        current.next = undefined;
        current.prev = undefined;
      }
      this.count--;
      return current.element;
    }
    return undefined;
  }
}

```

## 循环链表

```js
import { defaultEquals } from "../util";
import LinkedList from "./LinkedList.js";

export default class CircularLinkedList extends LinkedList {
  constructor(equalsFn = defaultEquals) {
    super(equalsFn);
  }

  insert(element, index) {
    if (index >= 0 && index <= this.count) {
      const node = new Node(element);
      let current = this.head;
      if (index === 0) {
        if (this.head === null) {
          this.head = node;
          node.next = this.head;
        } else {
          this.head = node;
          current = this.getElementAt(this.size());
          current.next = this.head;
        }
      } else {
        const previous = this.getElementAt(index - 1);
        current = previous.next;
        previous.next = node;
        node.next = currents;
      }
      this.count++;
      return true;
    }
    return false;
  }

  removeAt(index) {
    if (index >= 0 && index < this.size()) {
      let current = this.head;
      if (index === 0) {
        if (this.size() === 1) {
          this.head = null;
        } else {
          const removed = this.head;
          current = this.getElementAt(this.size());
          this.head = this.head.next;
          current.next = this.head;
          current = removed;
        }
      } else {
        const previous = this.getElementAt(index - 1);
        current = previous.next;
        previous.next = current.next;
      }

      this.count--;
      return current.element;
    }

    return undefined;
  }
}
```

## 有序链表

```js
import { Compare, defaultCompare, defaultEquals } from "../util";
import LinkedList from "./LinkedList.js";

export class SortLinkedList extends LinkedList {
  constructor(equalsFn = defaultEquals, compareFn = defaultCompare) {
    super(equalsFn);
    this.compareFn = compareFn;
  }

  insert(element, index = 0) {
    if (this.isEmpty()) {
      return super().insert(element, index);
    }
    const position = this.getIndexNextSortedElement(element);

    return super().insert(element, position);
  }

  getIndexNextSortedElement(element) {
    let current = this.head;
    let i = 0;
    for (; i < this.size(); i++) {
      const comp = this.compareFn(element, current.element);

      if (comp === Compare.LESS_THAN) {
        return i;
      }

      current = current.next;
    }
    return i;
  }
}

```

## 通过链表创建栈

```js
import DoublyLinkedList from "./DoublyLinkedList";

export default class StackLinkedList {
  constructor() {
    this.item = new DoublyLinkedList();
  }

  push(element) {
    this.item.push(element);
  }

  pop() {
    if (this.item.isEmpty()) {
      return undefined;
    }

    return this.item.removeAt(this.item.size() - 1);
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }

    return this.item.getElementAt(this.item.size() - 1);
  }

  isEmpty() {
    return this.item.isEmpty();
  }

  size() {
    return this.item.size();
  }

  clear() {
    this.item.clear();
  }

  toString() {
    return this.item.toString();
  }
}
```

