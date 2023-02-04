---
title: 学习JavaScript数据结构与算法（第3版）第十五章 算法复杂度
date: 2023-02-04 17:39:45
tags:
- [数据结构]
categories:
- [数据结构]
---

# 大 *O* 表示法

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2017.41.26.png)

## 理解大 *O* 表示法

如何衡量算法的效率？通常是用资源，例如 CPU（时间）占用、内存占用、硬盘占用和网络占用。当讨论大 *O* 表示法时，一般考虑的是 CPU（时间）占用

让我们试着用一些例子来理解大 *O* 表示法的规则。

 *O*(1) 

考虑以下函数。

```js
function increment(num){ 
 return ++num; 
}
```

假设运行 increment(1)函数，执行时间等于 *X*。如果再用不同的参数（例如 2）运行一次increment 函数，执行时间依然是 *X*。和参数无关，increment 函数的性能都一样。因此，我们说上述函数的复杂度是 *O*(1)（常数）。

 *O*(*n*) 

现在以第 13 章中实现的顺序搜索算法为例

```js
function sequentialSearch(array, value, equalsFn = defaultEquals) { 
 for (let i = 0; i < array.length; i++) { 
 if (equalsFn(value, array[i])) { // {1} 
 return i; 
 } 
 } 
 return -1; 
}
```

如果将含 10 个元素的数组（[1, ..., 10]）传递给该函数，假如搜索 1 这个元素，那么，第一次判断时就能找到想要搜索的元素。在这里我们假设每执行一次行{1}，开销是 1

现在，假如要搜索元素 11。行{1}会执行 10 次（迭代数组中所有的值，并且找不到要搜索的元素，因而结果返回-1）。如果行{1}的开销是 1，那么它执行 10 次的开销就是 10，10 倍于第一种假设。

现在，假如该数组有 1000 个元素（[1, ..., 1000]）。搜索 1001 的结果是行{1}执行了1000 次（然后返回-1）。

注意，sequentialSearch 函数执行的总开销取决于数组元素的个数（数组大小），而且也和搜索的值有关。如果是查找数组中存在的值，行{1}会执行几次呢？如果查找的是数组中不存在的值，那么行{1}就会执行和数组大小一样多次，这就是通常所说的最坏情况。

最坏情况下，如果数组大小是 10，开销就是 10；如果数组大小是 1000，开销就是 1000。可以得出 sequentialSearch 函数的时间复杂度是 *O*(*n*)，*n* 是（输入）数组的大小

回到之前的例子，修改一下算法的实现（最坏情况），使之计算开销

```js
function sequentialSearch(array, value, equalsFn = defaultEquals) { 
 let cost = 0; 
 for (let i = 0; i < array.length; i++) { 
 cost++; 
 if (equalsFn(value, array[i])) { 
 return i; 
 }
  } 
 console.log(`cost for sequentialSearch with input size ${array.length} is ${cost}`); 
 return -1; 
}
```

用不同大小的输入数组执行以上算法，可以看到不同的输出。

 *O*(*n*2 ) 

用冒泡排序做 *O*(*n*2)的例子。

```js
function bubbleSort(array, compareFn = defaultCompare) { 
 const { length } = array; 
 for (let i = 0; i < length; i++) { // {1} 
 for (let j = 0; j < length - 1; j++) { // {2} 
 if (compareFn(array[j], array[j + 1]) === Compare.BIGGER_THAN) { 
 swap(array, j, j + 1); 
 } 
 } 
 } 
 return array; 
}
```

假设行{1}和行{2}的开销分别是 1。修改算法的实现使之计算开销

```js
function bubbleSort(array, compareFn = defaultCompare) { 
 const { length } = array; 
 let cost = 0; 
 for (let i = 0; i < length; i++) { // {1} 
 cost++; 
 for (let j = 0; j < length - 1; j++) { // {2} 
 cost++; 
 if (compareFn(array[j], array[j + 1]) === Compare.BIGGER_THAN) { 
 swap(array, j, j + 1); 
 } 
 } 
 } 
 console.log(`cost for bubbleSort with input size ${length} is ${cost}`); 
 return array; 
}
```

如果用大小为 10 的数组执行 bubbleSort，开销是 100（102）。如果用大小为 100 的数组执行bubbleSort，开销就是 10 000（1002）。需要注意，我们每次增加输入的大小，执行都会越来越久

时间复杂度 *O*(*n*)的代码只有一层循环，而 *O*(*n*2)的代码有双层嵌套循环。如果算法有三层迭代数组的嵌套循环，它的时间复杂度很可能就是 *O*(*n*3)。

## 时间复杂度比较

我们可以创建一个表格来表示不同的时间复杂度	

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2018.06.17.png)

我们可以基于上表信息画一个图来表示不同的大 *O* 表示法的消耗。

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2018.07.33.png)

下表是常用数据结构的时间复杂度

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2018.08.43.png)

下表是图的时间复杂度

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2018.09.37.png)

下表是排序算法的时间复杂度

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2018.10.28.png)

下表是搜索算法的时间复杂度

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2018.11.21.png)
