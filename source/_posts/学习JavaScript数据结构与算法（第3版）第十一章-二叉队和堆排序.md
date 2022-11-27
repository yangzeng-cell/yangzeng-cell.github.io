---
title: 学习JavaScript数据结构与算法（第3版）第十一章 二叉队和堆排序
date: 2022-11-20 23:30:54
tags:
- [数据结构]
categories:
- [数据结构]
---

# 二叉队和堆排序

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

export function Swap(array, a, b) {
  const temp = array[a];
  array[a] = array[b];
  array[b] = temp;
}

//es6写法  有一个公开的问题表示解构操作比正常的赋值操作性能更差
export const swap1 = (array, a, b) =>
  ([array[a], array[b]] = [array[b], array[a]]);

```



## 小根堆

```js
import { Compare, defaultCompare, Swap } from "../util.js";

export default class MinHeap {
  constructor(compareFn = defaultCompare) {
    this.compareFn = compareFn;
    this.heap = [];
  }
  //获取左侧节点的位置
  getLeftIndex(index) {
    return 2 * index + 1;
  }
  //获取右侧结点的位置
  getRightIndex(index) {
    return 2 * index + 2;
  }
  //获取父节点的位置
  getParentIndex(index) {
    if (index === 0) {
      return undefined;
    }
    return Math.floor((index - 1) / 2);
  }
  /**
   * 这个方法向堆中插入一个新的值。如果插入成功，它返回 true，否则返回 false
   * @param {*} value
   */
  insert(value) {
    if (value != null) {
      this.heap.push(value);
      this.siftUp(this.heap.length - 1);
      return true;
    }

    return false;
  }
  //表示我们将要将这个值和它的父节点进行交换，直到父节点小于这个插入的值。这个上移操作也被称为 up head、percolate up、bubble up、heapify up 或 cascade up
  siftUp(index) {
    //获取父节点的索引
    let parent = this.getParentIndex(index);
    //小根堆做法
    while (
      index > 0 &&
      this.compareFn(this.heap[parent], this.heap[index]) ===
        Compare.BIGGER_THAN
    ) {
      Swap(this.heap, paren, index);
      index = parent;
      parent = this.getParentIndex(index);
    }
  }

  /**
   * 移除最小值(小根堆)或者最大值(大根堆)并且返回该值
   */
  extract() {
    if (this.isEmpty()) {
      return undefined;
    }
    if (this.size() === 1) {
      return this.heap.shift();
    }
    const removedValue = this.heap.shift();
    this.siftDown(0);
    return removedValue;
  }

  siftDown(index) {
    let element = index;
    const left = this.getLeftIndex(index);
    const right = this.getRightIndex(index);
    const size = this.size();

    if (
      left < size &&
      this.compareFn(this.heap[element], this.heap[left]) ===
        Compare.BIGGER_THAN
    ) {
      element = left;
    }

    if (
      left < size &&
      this.compareFn(this.heap[element], this.heap[right]) ===
        Compare.BIGGER_THAN
    ) {
      element = right;
    }

    if (index !== element) {
      Swap(this.heap, index, element);
      this.siftDown(element);
    }
  }

  /**
   * 返回小根堆的最小值或者大根堆的最大值但不会删除该值
   */
  findMinimum() {
    return this.size() ? this.heap[0] : undefined;
  }

  size() {
    return this.heap.length;
  }

  isEmpty() {
    return this.size() === 0;
  }
}

```

