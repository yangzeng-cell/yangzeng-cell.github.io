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

## NP 完全理论概述

一般来说，如果一个算法的复杂度为 *O*(*n**k*)，其中 *k* 是常数，我们就认为这个算法是高效的，这就是多项式算法。

对于给定的问题，如果存在多项式算法，则计为 *P*（polynomial，多项式）

还有一类 NP（nondeterministic polynomial，非确定性多项式）算法。如果一个问题可以在多项式时间内验证解是否正确，则计为 *NP*。

如果一个问题存在多项式算法，自然可以在多项式时间内验证其解。因此，所有的 *P* 都是*NP*。然而，*P* = *NP* 是否成立，仍然不得而知。

NP 问题中最难的是 NP 完全问题。如果满足以下两个条件，则称决策问题 *L* 是 NP 完全的：

(1) *L* 是 NP 问题，也就是说，可以在多项式时间内验证解，但还没有找到多项式算法；

(2) 所有的 NP 问题都能在多项式时间内归约为 *L*。

为了理解问题的归约，考虑两个决策问题 *L*和 *M*。假设算法 *A*可以解决问题 *L*，算法 *B*可以验证输入 *y*是否为 *M*的解。目标是找到一个把 *L*转化为 *M*的方法，使得算法 *B*可以用于构造算法 *A*

还有一类问题，只需满足 NP 完全问题的第二个条件，称为 NP 困难问题。因此，NP 完全问题也是 NP 困难问题的子集。

*P* = *NP* 是否成立，是计算机科学中最重要的难题之一。如果能找到答案，对密码学、算法研究、人工智能等诸多领域都会产生重大影响。

下面是满足 *P*<>*NP* 时，P、NP、NP 完全和 NP 困难问题的欧拉图。

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-05%2001.20.53.png)

非 NP 完全的 NP 困难问题的例子有停机问题和布尔可满足性问题（SAT）。

NP 完全问题的例子有子集和问题、旅行商问题、顶点覆盖问题，等等。

关于这些问题，详情请查阅 https://en.wikipedia.org/wiki/NP-completeness

## 不可解问题与启发式算法

我们提到的有些问题是不可解的。然而，仍然有办法在符合要求的时间内找到一个近似解。启发式算法就是其中之一。启发式算法得到的未必是最优解，但足够解决问题了

启发式算法的例子有局部搜索、遗传算法、启发式导航、机器学习等。详情请查阅https://en.wikipedia.org/wiki/Heuristic_(computer_science)

启发式算法可以很巧妙地解决一些问题。你可以尝试把研究启发式算法作为学士或硕士学位的论文主题。

# 用算法娱乐身心

UVa Online Judge（http://uva.onlinejudge.org/）：这个网站包含了世界各大赛事的题目，包括 由 IBM 赞助的 ACM 国际大学生程序竞赛（ICPC。若你依然在校，应尽量参与这项赛事，如果团队获胜，则有可能免费享受一次国际旅行）。这个网站包括了成百上千的题目，可以应用本书所学的算法。

Sphere Online Judge（http://www.spoj.com/）： 这个网站和 UVa Online Judge 差不多，但支持用更多语言解题（包括 JavaScript）。

Coderbyte（http://coderbyte.com/）：这个网站包含了可以用 JavaScript 解答的题目（简单、中等难度和非常困难）。

Project Euler（https://projecteuler.net/）：这个网站包含了一系列数学/计算机的编程题目。你所要做的就是输入那些题目的答案，不过我们可以用算法来找到正确的解答。

HackerRank（https://www.hackerrank.com）：这个网站包含 16 个类别的挑战（可以应用本书中的算法和更多其他算法）。它也支持 JavaScript 和其他语言。

CodeChef（http://www.codechef.com/）：这个网站包含一些题目，并会举办在线比赛。

Top Coder（http://www.topcoder.com/）：此网站会举办算法联赛，这些联赛通常由 NASA、Google、Yahoo!、Amazon 和 Facebook 这样的公司赞助。参加其中一些赛事，你可以获得到赞助公司工作的机会，而参与另一些赛事会赢得奖金。这个网站也提供很棒的解题和算法教程。
