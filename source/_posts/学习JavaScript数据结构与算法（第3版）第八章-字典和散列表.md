---
title: 学习JavaScript数据结构与算法（第3版）第八章 字典和散列表
date: 2022-11-20 18:32:16
tags:
- [数据结构]
categories:
- [数据结构]
---

# 字典和散列表

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

## Dictionary

```js
import { ValuePair } from "../linked-list-models.js";
import { defaultToString } from "../util.js";

class Dictionary {
  constructor(toStrFn = defaultToString) {
    this.toStrFn = toStrFn;
    this.table = {};
  }
  /**
   * 向字典中添加新元素。如果 key 已经存在，那么已存在的 value 会 被新的值覆盖。
   * @param {*} key
   * @param {*} value
   */
  set(key, value) {
    if (key != null && value != null) {
      this.table[this.toStrFn(key)] = new ValuePair(key, value);
      return true;
    }
    return false;
  }
  //通过使用键值作为参数来从字典中移除键值对应的数据值
  remove(key) {
    if (this.hasKey(key)) {
      delete this.table[this.toStrFn(key)];
      return true;
    }
    return false;
  }

  // 如果某个键值存在于该字典中，返回 true，否则返回 false
  hasKey(key) {
    return this.table[this.toStrFn(key)] != null;
  }

  //  通过以键值作为参数查找特定的数值并返回
  get(key) {
    const valuePair = this.table[this.toStrFn[key]];

    return valuePair == null ? "undefined" : valuePair.value;
  }

  //删除该字典中的所有值
  clear() {
    this.table = {};
  }

  //返回字典所包含值的数量。与数组的 length 属性类似
  size() {
    return Object.keys(this.table).length;
  }

  //在 size 等于零的时候返回 true，否则返回 false

  isEmpty() {
    return this.size() === 0 ? true : false;
  }

  // 将字典所包含的所有键名以数组形式返回
  keys() {
    return this.keyValues().map((valuePair) => valuePair.key);
  }

  // 将字典所包含的所有数值以数组形式返回
  values() {
    return this.keyValues().map((valuePair) => valuePair.value);
  }

  //将字典中所有[键，值]对返回
  keyValues() {
    // return Object.values(this.table);
    const valuePairs = [];

    for (let key in this.table) {
      if (this.hasKey(key)) {
        valuePairs.push(this.table[key]);
      }
    }

    return valuePairs;
  }

  //迭代字典中所有的键值对。callbackFn 有两个参数:key 和value。该方法可以在回调函数返回 false 时被中止(和 Array 类中的 every 方法相似)。
  foreach(callbackFN) {
    const valuePairs = this.keyValues();

    for (let i = 0; i < valuePairs.length; i++) {
      const result = callbackFN(valuePairs[i].key, valuePairs[i].value);
      if (result === false) {
        break;
      }
    }
  }

  toString() {
    if (this.isEmpty()) {
      return "";
    }

    const valuePairs=this.keyValues()
    let str=valuePairs[0].toString()
    for(let i=0=1;i<valuePairs.length;i++){
      str+=`,${valuePairs[i].toString()}`
    }
    return str
  }

}
```

## 散列表

HashTable 类，也叫 HashMap 类，它是 Dictionary 类的一种散列表实现方式。

```js
import { ValuePair } from "../linked-list-models";
import { defaultToString } from "../util";
/**
 * 基础版，相同的hashkey的话会被覆盖
 */
export default class HashTable {
  constructor(toStrFn = defaultToString) {
    this.toStrFn = toStrFn;
    this.table = {};
  }

  /**
   * 向散列表增加一个新的项（也能更新散列表）
   * @param {*} key
   * @param {*} value
   */
  put(key, value) {
    if (key == null || value == null) {
      return false;
    }

    const position = this.hashCode(key);

    this.table[position] = new ValuePair(position, value);

    return true;
  }
  // 根据键值从散列表中移除值
  remove(key) {
    const hash = this.hashCode[key];
    const valuePair = this.table[hash];

    if (valuePair != null) {
      delete this.table[hash];
      return true;
    }

    return false;
  }
  // 返回根据键值检索到的特定的值
  get(key) {
    const valuePair = thia.table[this.hashCode(key)];
    return valuePair == null ? undefined : valuePair.value;
  }

  loseloseHashCode(key) {
    if (typeof key === "number") {
      return key;
    }

    const tableKey = this.toStrFn(key);
    let hash = 0;
    for (let i = 0; i < tableKey.length; i++) {
      hash += this.tableKey.charCodeAt(tableKey[i]);
    }

    return hash % 37;
  }

  hashCode(key) {
    return this.loseloseHashCode(key);
  }
}

```

## 处理散列表中的冲突

分离链接法包括为散列表的每一个位置创建一个链表并将元素存储在里面。它是解决冲突的最简单的方法，但是在 HashTable 实例之外还需要额外的存储空间。

```js
import { defaultToString } from "../util";
import LinkedList from "../LinkedList/LinkedList";
import { ValuePair } from "../linked-list-models";
export default class HashTableSeparateChaining {
  constructor(toStrFn = defaultToString) {
    this.toStrFn = toStrFn;
    this.table = {};
  }
  loseloseHashCode(key) {
    if (typeof key === "number") {
      return key;
    }

    const tableKey = this.toStrFn(key);
    let hash = 0;
    for (let i = 0; i < tableKey.length; i++) {
      hash += this.tableKey.charCodeAt(tableKey[i]);
    }

    return hash % 37;
  }

  hashCode(key) {
    return this.loseloseHashCode(key);
  }

  put(key, value) {
    if (key != null || value != null) {
      const position = this.hashCode(key);

      if (this.table[position] == null) {
        this.table[position] = new LinkedList();
      }
      const valuePair = new ValuePair(key, value);

      this.table[position].push(valuePair);

      return true;
    }
    return false;
  }

  get(key) {
    const position = this.hashCode(key);

    const linkedList = this.table[position];

    if (linkedList != null && !linkedList.isEmpty()) {
      let current = linkedList.getHead();
      while (current != null) {
        if (current.element.key === key) {
          return current.element.value;
        }
        current = current.next;
      }
    }
    return undefined;
  }

  remove(key) {
    const position = this.hashCode(key);

    const linkedList = this.table[position];

    if (linkedList != null && !linkedList.isEmpty()) {
      let current = linkedList.getHead();
      while (!current) {
        if (current.element.key === key) {
          linkedList.remove(current.element);
          if (linkedList.isEmpty()) {
            delete this.table[position];
          }
          return true;
        }
        current = current.next;
      }
    }
    return false;
  }
}

```

另一种解决冲突的方法是线性探查。之所以称作线性，是因为它处理冲突的方法是将元素直接存储到表中，而不是在单独的数据结构中。

```js
import { defaultToString } from "../util";
import LinkedList from "../LinkedList/LinkedList";
import { ValuePair } from "../linked-list-models";

export default class HashTableLinearProbing {
  constructor(toStrFn = defaultToString) {
    this.toStrFn = toStrFn;
    this.table = {};
  }
  loseloseHashCode(key) {
    if (typeof key === "number") {
      return key;
    }

    const tableKey = this.toStrFn(key);
    let hash = 0;
    for (let i = 0; i < tableKey.length; i++) {
      hash += this.tableKey.charCodeAt(tableKey[i]);
    }

    return hash % 37;
  }

  //另外一种散列函数
  djb2HashCode(key) {
    const tableKey = this.toStrFn(key);

    let hash = 5381;

    for (let i = 0; i < tableKey.length; i++) {
      hash = hash * 33 + tableKey.charCodeAt(i);
    }
    return hash % 1013;
  }

  hashCode(key) {
    return this.loseloseHashCode(key);
  }

  put(key, value) {
    if (key != null && value != null) {
      const position = this.hashCode(key);
      if (!this.table[position]) {
        this.table[position] = new ValuePair(key, value);
      } else {
        let index = position + 1;
        while (this.table[index]) {
          index++;
        }
        this.table[index] = new ValuePair(key, value);
      }
      return true;
    }
    return false;
  }

  get(key) {
    const position = this.hashCode(key);
    if (!this.table[position]) {
      if (this.table[position].key === key) {
        return this.table[position].value;
      } else {
        let index = position + 1;
        while (this.table[index] != null && this.table[index].key !== key) {
          index++;
        }
        if (this.table[index] != null && this.table[index].key === key) {
          return this.table[index].value;
        }
      }
    }

    return undefined;
  }

  remove(key) {
    const position = this.hashCode(key);

    if (!this.table[position]) {
      if (this.table[position].key === key) {
        delete this.table[position];
        this.verifyRemoveSideEffect(key, position);
        return true;
      } else {
        let index = position + 1;
        while (this.table[index] != null && this.table[index].key !== key) {
          index++;
        }
        if (this.table[index] != null && this.table[index].key === key) {
          delete this.table[index];
          this.verifyRemoveSideEffect(key, index);
          return true;
        }
      }
    }
    return false;
  }

  verifyRemoveSideEffect(key, removedPosition) {
    const hash = this.hashCode(key);

    let index = removedPosition + 1;

    while (this.table[index] != null) {
      const posHash = this.table[this.table[index].key];

      if (posHash <= hash || posHash <= removedPosition) {
        this.table[removedPosition] = this.table[index];
        delete this.table[index];

        removedPosition = index;
      }
      index++;
    }
  }
}


```

