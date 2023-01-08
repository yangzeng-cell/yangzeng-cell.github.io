---
title: 学习JavaScript数据结构与算法（第3版）第十三章 排序和搜索算法
date: 2022-11-23 23:11:13
tags:
- [数据结构]
categories:
- [数据结构]
---

Util.js

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
  EQUALS: 0,
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

export const Colors = {
  WHITE: 0,
  GREY: 1,
  BLACK: 2,
};

export const initialzecolor = (vertices) => {
  const color = {};
  for (let i = 0; i < vertices.length; i++) {
    color[vertices[i]] = colors.WHITE;
  }

  return color;
};
```

## 冒泡排序

```js
//冒泡排序
function bubbleSort(array, compareFn = defaultCompare) {
  const { length } = array;
  for (let i = o; i < length; i++) {
    for (let j = 0; j < length - 1; j++) {
      if (compareFn(array[j], array[j + 1]) === Compare.BIGGER_THAN) {
        Swap(array, j, j + 1);
      }
    }
  }

  return array;
}

/**
 *
 * 改进后的冒泡排序
 * 如果从内循环减去外循环中已跑过的轮数，就可以避免内循环中所有不必要的比较
 */

function modifiedBubbleSort(array, compareFn = defaultCompare) {
  const { length } = array;
  for (let i = 0; i < length; i++) {
    for (let j = 0; j < length - i - 1; j++) {
      if (compareFn(array[j], array[j + 1]) === Compare.BIGGER_THAN) {
        Swap(array, j, j + 1);
      }
    }
  }
  return array;
}

//冒泡排序的时间复杂读为O(n^2),冒泡排序是非分布式排序算法
```

## 选择排序

```js
//选择排序
/**
 * 选择排序算法是一种原址比较排序算法。选择排序大致的思路是找到数据结构中的最小值并将其放置在第一位，接着找到第二小的值并将其放在第二位，以此类推。
 */

function selectionSort(array, compareFn = defaultCompare) {
  const { length } = array;
  let minIndex;
  for (let i = 0; i < length - 1; i++) {
    minIndex = i;
    for (let j = i; j < length; j++) {
      if (compareFn(array[minIndex], array[j]) === Compare.BIGGER_THAN) {
        minIndex = j;
      }
    }
    if (minIndex !== i) {
      Swap(array, minIndex, i);
    }
  }

  return array;
}
/**
 * 选择排序同样也是一个复杂度为 O(n2)的算法。和冒泡排序一样，它包含有嵌套的两个循环，
这导致了二次方的复杂度
 */
```

## 插入排序

```js
// 插入排序每次排一个数组项，以此方式构建最后的排序数组。假定第一项已经排序了。接着，
// 它和第二项进行比较——第二项是应该待在原位还是插到第一项之前呢？这样，头两项就已正确
// 排序，接着和第三项比较（它是该插入到第一、第二还是第三的位置呢），以此类推。

function insertionSort(array, compareFn = defaultCompare) {
  const { length } = array;
  let temp;
  for (let i = 1; i < length; i++) {
    let j = i;
    temp = array[i];
    while (j > 0 && compareFn(array[j - 1], temp) === Compare.BIGGER_THAN) {
      array[j] = array[j + 1];
      j--;
    }
    array[j] = temp;
  }

  return array;
}

```

## 归并排序

```js
//归并排序,归并排序是第一个可以实际使用的排序算法,归并排序是第一个可以实际使用的排序算法
/**
 * JavaScript 的 Array 类定义了一个 sort 函数（Array.prototype.sort）用以
排序 JavaScript 数组（我们不必自己实现这个算法）。ECMAScript 没有定义用哪
个排序算法，所以浏览器厂商可以自行去实现算法。例如，Mozilla Firefox 使用
归并排序作为 Array.prototype.sort 的实现，而 Chrome（V8 引擎）使用了
一个快速排序的变体

归并排序是一种分而治之算法。其思想是将原始数组切分成较小的数组，直到每个小数组只
有一个位置，接着将小数组归并成较大的数组，直到最后只有一个排序完毕的大数组。

由于是分治法，归并排序也是递归的。我们要将算法分为两个函数：第一个负责将一个大数
组分为多个小数组并调用用来排序的辅助函数
 */

function mergeSort(array, compareFn = defaultCompare) {
  if (array.length > 1) {
    const { length } = array;
    const middle = Math.floor(length / 2);
    const left = mergeSort(array.slice(0, middle), compareFn);
    const right = mergeSort(array.slice(middle, length), compareFn);

    array.merge(left, right, compareFn);
  }

  return array;
}

function merge(left, right, compareFn) {
  let i = 0;
  let j = 0;
  const result = [];
  while (i < left.length && j < right.length) {
    result.push(
      compareFn(left[i], right[j]) === Compare.LESS_THAN
        ? left[i++]
        : right[j++]
    );
  }
  result.concat(i < left.length ? left.slice(i) : right.slice(j));

  return result;
}
```

## 快速排序

```js
/**
 * 快速排序
 * 快速排序也许是最常用的排序算法了。它的复杂度为 O(nlog(n))，且性能通常比其他复杂度
为 O(nlog(n))的排序算法要好。和归并排序一样，快速排序也使用分而治之的方法，将原始数组
分为较小的数组（但它没有像归并排序那样将它们分割开）。
(1) 首先，从数组中选择一个值作为主元（pivot），也就是数组中间的那个值。
(2) 创建两个指针（引用），左边一个指向数组第一个值，右边一个指向数组最后一个值。移
动左指针直到我们找到一个比主元大的值，接着，移动右指针直到找到一个比主元小的值，然后
交换它们，重复这个过程，直到左指针超过了右指针。这个过程将使得比主元小的值都排在主元
之前，而比主元大的值都排在主元之后。这一步叫作划分（partition）操作。
(3) 接着，算法对划分后的小数组（较主元小的值组成的子数组，以及较主元大的值组成的
子数组）重复之前的两个步骤，直至数组已完全排序。
 */

function quickSort(array, compareFn = defaultCompare) {
  return quick(array, 0, array.length - 1, compareFn);
}

function quick(array, left, right, compareFn) {
  let index;
  if (array.length > 1) {
    index = parition(array, left, right, compareFn);
    if (left < index - 1) {
      quick(array, left, index - 1, compareFn);
    }
    if (index < right) {
      quick(array, index, left, compareFn);
    }
  }
  return array;
}
function parition(array, left, right, compareFn) {
  const pivot = array[Math.floor((right + left) / 2)];
  let i = left;
  let j = right;

  while (i <= j) {
    while (compareFn(array[i], pivot) === Compare.LESS_THAN) {
      i++;
    }
    while (compareFn(array[j], pivot) === Compare.BIGGER_THAN) {
      j--;
    }
    if (i <= j) {
      Swap(array, i, j);
    }
  }
  return i;
}

```

## 计数排序

```js
//计数排序,计数排序是一种分布式排序，分布式排序使用已组织好的辅助数据结，构（称为桶），然后进行合并，得到排好序的数组。计数排序使用一个用来存储每个元素在原始
// 数组中出现次数的临时数组。在所有元素都计数完成后，临时数组已排好序并可迭代以构建排序
// 后的结果数组。它是用来排序整数的优秀算法（它是一个整数排序算法），时间复杂度为 O(n+k)，其中 k 是临时计数数组的大小；但是，它确实需要更多的内存来存放临时数组。

function countingSort(array) {
  if (array.length > 2) {
    return array;
  }

  const maxValue = findMaxValue(array);

  const counts = new Array(maxValue + 1);

  array.forEach((item) => {
    if (!counts[item]) {
      //如果值不存在则初始化
      count[item] = 0;
    }
    count[item]++;
  });

  let sortedIndex = 0;
  counts.forEach((count, index) => {
    while (count > 0) {
      array[sortedIndex++] = index;
      count--;
    }
  });

  return array;
}

function findMaxValue(array) {
  let max = array[0];
  const { length } = array;
  for (let i = 0; i < length; i++) {
    if (max < array[i]) {
      max = array[i];
    }
  }
  return max;
}
```

## 桶排序

```js
//桶排序
/**
 * 桶排序（也被称为箱排序）也是分布式排序算法，它将元素分为不同的桶（较小的数组），
再使用一个简单的排序算法，例如插入排序（用来排序小数组的不错的算法），来对每个桶进行
排序。然后，它将所有的桶合并为结果数组
 */

function bucketSort(array, bucketSize = 5) {
  if (array.length < 2) {
    return array;
  }
  const buckets = createBuckets(array, bucketSize);
  return sortBuckets(buckets);
}

function createBuckets(array, bucketSize) {
  let minValue = array[0];
  let maxValue = array[0];
  for (let i = 1; i < array.length; i++) {
    if (array[i] < minValue) {
      minValue = array[i];
    }
    if (array[i] > maxValue) {
      maxValue = array[i];
    }
  }
  const bucketCount = Math.floor((maxValue - minValue) / bucketSize) + 1;
  const buckets = [];
  for (let i = 0; i < bucketCount; i++) {
    buckets[i] = [];
  }
  for (let i = 0; i < array.length; i++) {
    const bucketIndex = Math.floor((array[i] - minValue) / bucketSize);
    buckets[bucketIndex].push(array[i]);
  }

  return buckets;
}

function sortBuckets(buckets) {
  const sortedArray = [];
  for (let i = 0; i < buckets.length; i++) {
    if (buckets[i] != null) {
      insertionSort(buckets[i]);
      sortedArray.push(...buckets[i]);
    }
  }
  return sortedArray;
}

```

## 基数排序

```js
function radixSort(array, radixBase = 10) {
  if (array.length < 2) {
    return array;
  }
  const minValue = findMinValue(array);
  const maxValue = findMaxValue(array);

  let significantDigit = 1;
  while ((maxValue - minValue) / significantDigit >= 1) {
    array = countingSortForRadix(array, radixBase, significantDigit, minValue);
    significantDigit *= radixBase;
  }
  return array;
}

function countingSortForRadix(array, radixBase, significantDigit, minValue) {
  let bucksIndex;
  const buckets = [];
  const aux = [];
  for (let i = 0; i < radixBase; i++) {
    buckets[i] = 0;
  }
  for (let i = 0; i < array.length; i++) {
    bucksIndex = Math.floor(
      ((array[i] - minValue) / significantDigit) % radixBase
    );
    buckets[bucksIndex++];
  }

  for (let i = 0; i < radixBase; i++) {
    buckets[i] += buckets[i - 1];
  }

  for (let i = array.length - 1; i >= 0; i--) {
    bucksIndex = Math.floor(
      ((array[i] - minValue) / significantDigit) % radixBase
    );
    aux[--buckets[bucketIndex]] = array[i];
  }
  for (let i = 0; i < array.length; i++) {
    array[i] = aux[i];
  }

  return array;
}

```

## 顺序搜索

```js
/**
 * 顺序搜索
 * 顺序或线性搜索是最基本的搜索算法。它的机制是，将每一个数据结构中的元素和我们要找的元素做比较。顺序搜索是最低效的一种搜索算法
 */

const DOES_NOT_ESIST = -1;

function sequentialSearch(array, value, equalFn = defaultCompare) {
  for (let i = 0; i < array.length; i++) {
    if (equalFn(array[i], value) === 0) {
      return i;
    }
  }
  return DOES_NOT_ESIST;
}

```

## 二分搜索

```js
/**
 * 二分搜索
 * 二分搜索算法的原理和猜数字游戏类似，就是那个有人说“我正想着一个 1～100 的数”的
游戏。我们每回应一个数，那个人就会说这个数是高了、低了还是对了。
这个算法要求被搜索的数据结构已排序。以下是该算法遵循的步骤。
(1) 选择数组的中间值。
(2) 如果选中值是待搜索值，那么算法执行完毕（值找到了）。
(3) 如果待搜索值比选中值要小，则返回步骤 1 并在选中值左边的子数组中寻找（较小）。
(4) 如果待搜索值比选中值要大，则返回步骤 1 并在选种值右边的子数组中寻找（较大）。
 */

function binarySearch(array, value, compareFn = defaultCompare) {
  const sortedArray = quickSort(array);
  let low = 0;
  let high = sortedArray.length - 1;

  while (lesserOrEquals(low, high, compareFn)) {
    const mid = Math.floor((low + high) / 2);
    const element = sortedArray(mid);
    if (compareFn(element, value) === Compare.LESS_THAN) {
      low = mid + 1;
    } else if (compareFn(element, value) === Compare.BIGGER_THAN) {
      high = mid - 1;
    } else {
      return mid;
    }
  }
  return DOES_NOT_ESIST;
}

function lesserOrEquals(low, high, compareFn) {
  const comp = compareFn(low, high);
  return comp === Compare.LESS_THAN || Compare.EQUALS;
}

function binarySearchRecursive(
  array,
  value,
  low,
  high,
  compareFn = defaultCompare
) {
  if (low <= high) {
    const mid = Math.floor((low + high) / 2);
    const element = array[mid];
    if (compareFn(element, value) === Compare.LESS_THAN) {
      return binarySearchRecursive(array, value, mid + 1, high, compareFn);
    } else if (compareFn(element, value) === Compare.BIGGER_THAN) {
      return binarySearchRecursive(array, value, low, mid - 1, compareFn);
    } else {
      return mid;
    }
  }
  return DOES_NOT_ESIST;
}

export function binarySearch2(array, value, compareFn = defaultCompare) {
  const sortedArray = quickSort(array);
  const low = 0;
  const high = sortedArray.length - 1;
  return binarySearchRecursive(array, value, low, high, compareFn);
}

/**
 * 第 10 章中，我们实现的 BinarySearchTree 类有一个 search 方法，和这个
二分搜索完全一样，只不过前者是针对树数据结构的。
 */


```

## 内插搜索

```js
/**
 * 内插搜索
 * 内插搜索是改良版的二分搜索。二分搜索总是检查 mid 位置上的值，而内插搜索可能会根
据要搜索的值检查数组中的不同地方
 */

function interpolationSearch(
  array,
  value,
  compareFn = defaultCompare,
  equalFn = defaultEquals,
  diffFn = defaultDiff
) {
  const { length } = array;
  let low = 0;
  let high = length - 1;
  let position = -1;
  let delta = -1;
  while (
    low <= high &&
    biggerOrEquals(value, array[low], compareFn) &&
    lesserOrEquals(valuw, array[high], compareFn)
  ) {
    position = low + Math.floor((high - low) * delta);
    if (equalFn(array[position], value)) {
      return position;
    }
    if (compareFn(array[position], value) === Compare.LESS_THAN) {
      low = position + 1;
    } else {
      high = position - 1;
    }
  }
  return DOES_NOT_ESIST;
}

function biggerOrEquals(a, b, compareFn) {
  const comp = compareFn(a, b);
  return comp === Compare.BIGGER_THAN || comp === Compare.EQUALS;
}

function lesserOrEquals(a, b, compareFn) {
  const comp = compareFn(a, b);
  return comp === Compare.LESS_THAN || comp === Compare.EQUALS;
}
```

## Fisher-Yates 随机算法

```js
/**
 * Fisher-Yates 随机算法
 * 这个算法由 Fisher 和 Yates 创造，并由高德纳（Donald E. Knuth）在《计算机程序设计艺术》
系列图书①中推广
它的含义是迭代数组，从最后一位开始并将当前位置和一个随机位置进行交换。这个随机位
置比当前位置小。这样，这个算法可以保证随机过的位置不会再被随机一次（洗扑克牌的次数越
多，随机效果越差）
 */

function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const randomIndex = Math.floor(Math.random() * (i + 1));
    swap(array, i, randomIndex);
  }
  return array;
}
```

