---
title: 学习JavaScript数据结构与算法（第3版）第七章 集合
date: 2022-11-20 18:27:38
tags:
- [数据结构]
categories:
- [数据结构]
---

# 集合

```js
class Set1 {
  constructor() {
    this.items = {};
  }
  // 向集合添加一个新元素
  add(element) {
    if (!this.has(element)) {
      this.items[element] = element;
      return true;
    }

    return false;
  }

  // 从集合移除一个元素

  delete(element) {
    if (this.has(element)) {
      delete this.items[element];
      return true;
    }
    return false;
  }

  // 如果元素在集合中，返回 true，否则返回 false
  has(element) {
    // return element in this.items;
    // 优化
    /**
     * 我们也可以在代码中使用 this.items.hasOwnProperty(element)。但是， 如果这样的话，代码检查工具如 ESLint 会抛出一个错误。错误的原因为不是所 有的对象都继承了 Object.prototype，甚至继承了 Object.prototype 的对 象上的 hasOwnProperty 方法也有可能被覆盖，导致代码不能正常工作。要避 免出现任何问题，使用 Object.prototype.hasOwnProperty.call 是更安 全的做法。
     */
    return Object.prototype.hasOwnProperty.call(this.items, element);
  }

  // 移除集合中的所有元素

  clear() {
    this.items = {};
  }

  // 返回集合所包含元素的数量。它与数组的 length 属性类似

  size() {
    return Object.keys(this.items).length;
  }

  // 返回一个包含集合中所有值(元素)的数组
  values() {
    return Object.values(this.items);
  }

  // union 并集:对于给定的两个集合，返回一个包含两个集合中所有元素的新集合
  union(otherElements) {
    const set = new Set1();
    const otherValue = otherElements.values();
    for (let i = 0; i < otherValue.length; i++) {
      set.add(otherValue[i]);
    }

    const values = this.values();

    for (let i = 0; i < values.length; i++) {
      set.add(values[i]);
    }
    return set;
  }

  intersection(otherSet) {
    const set = new Set1();

    // const values = this.values();
    // for (let i = 0; i < values.length; i++) {
    //   if (otherSet.has(values[i])) {
    //     set.add(values[i]);
    //   }
    // }

    //针对长度不同的set做交集的时候的优化

    let values = this.values();
    let otherValues = otherSet.values();
    let biggerSet = values;
    let smallerSet = otherValues;

    if (biggerSet.length - smallerSet.length < 0) {
      let middle = biggerSet;
      biggerSet = smallerSet;
      smallerSet = middle;
    }

    smallerSet.forEach((item) => {
      if (biggerSet.includes(item)) {
        set.add(item);
      }
    });
    return set;
  }

  /**
   * 对于给定的两个集合，返回一个包含所有存在于第一个集合且不存在于第二个集
合的元素的新集合
   * @param {*} otherSet 
   * @returns 
   */
  difference(otherSet) {
    const differenceSet = new Set1();
    this.values().forEach((item) => {
      if (!otherSet.has(item)) {
        differenceSet.add(item);
      }
    });

    return differenceSet;
  }

  isSubsetOf(otherSet) {
    if (this.size() > otherSet.size()) {
      return false;
    }

    let isSubset = true;

    this.values().every((item) => {
      if (!otherSet.has(item)) {
        isSubset = false;
        return false;
      }
      return true;
    });

    return isSubset;
  }
}

// const set = new Set1();
// set.add(1);
// console.log(set.values()); // 输出[1] console.log(set.has(1)); // 输出true console.log(set.size()); // 输出1
// set.add(2);
// console.log(set.values()); // 输出[1, 2] console.log(set.has(2)); // 输出true console.log(set.size()); // 输出2
// set.delete(1);
// console.log(set.values()); // 输出[2]
// set.delete(2);
// console.log(set.values()); // 输出[]

// const setA = new Set1();
// setA.add(1);
// setA.add(2);
// setA.add(3);
// const setB = new Set1();
// setB.add(3);
// setB.add(4);
// setB.add(5);
// setB.add(6);
// const unionAB = setA.union(setB);
// console.log(unionAB.values());

// const setA = new Set1();
// setA.add(1);
// setA.add(2);
// setA.add(3);
// const setB = new Set1();
// setB.add(2);
// setB.add(3);
// setB.add(4);
// const intersectionAB = setA.intersection(setB);
// console.log(intersectionAB.values());

// const setA = new Set1();
// setA.add(1);
// setA.add(2);
// const setB = new Set1();
// setB.add(1);
// setB.add(2);
// setB.add(3);
// const setC = new Set1();
// setC.add(2);
// setC.add(3);
// setC.add(4);
// console.log(setA.isSubsetOf(setB));
// console.log(setA.isSubsetOf(setC));

export default Set1;

```

