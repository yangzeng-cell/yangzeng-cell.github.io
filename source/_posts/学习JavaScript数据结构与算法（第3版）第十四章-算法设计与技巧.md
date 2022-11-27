---
title: 学习JavaScript数据结构与算法（第3版）第十四章 算法设计与技巧
date: 2022-11-25 22:44:56
tags:
- [数据结构]
categories:
- [数据结构]
---

# 分而治之

了归并和排序算法。两者的共同点在于它们都是分而治之算法。分而治之是算法设计中的一种方法。它将一个问题分成多个和原问题相似的小问题，递归解决小问题，再将解决方式合并以解决原来的问题

分而治之算法可以分成三个部分

(1) 分解原问题为多个子问题（原问题的多个小实例）。

(2) 解决子问题，用返回解决子问题的方式的递归算法。递归算法的基本情形可以用来解决子

问题。

(3) 组合这些子问题的解决方式，得到原问题的解。

## 二分搜索

二分搜索也可以用分而治之的方式来实现

```js
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
```

在上面的算法中，我们有两个函数：binarySearch 和 binarySearchRecursive。binarySearch 函数用来暴露给开发者进行二分搜索。binarySearchRecursive 是分而治之算法。我们将 low 参数以 0 传递，将 high 参数以 sortedArray.length - 1 传递，来在已排序的数组中进行搜索。在计算 mid 元素的索引值后，我们确定待搜索的值比 mid 大还是小。如果小（行{1}）或大（行{2}），就再次调用 binarySearchRecursive 函数，但是这次，我们在子数组中进行搜索，改变 low 或 high 参数（不同于我们在第 13 章中那样移动指针）。如果不大也不小，表示我们找到了这个值（行{3}）并且这就是一种基本情形。还有一种情况是 low比 high 要大，这表示算法没有找到这个值（行{4}）。

# 动态规划

动态规划（dynamic programming，DP）是一种将复杂问题分解成更小的子问题来解决的优化技术。

注意，动态规划和分而治之是不同的方法。分而治之方法是把问题分解成相互独立的子问题，然后组合它们的答案，而动态规划则是将问题分解成相互依赖的子问题

用动态规划解决问题时，要遵循三个重要步骤：

(1) 定义子问题；

(2) 实现要反复执行来解决子问题的部分；

(3) 识别并求解出基线条件。

能用动态规划解决的一些著名问题如下。

背包问题：给出一组项，各自有值和容量，目标是找出总值最大的项的集合。这个问题的限制是，总容量必须小于等于“背包”的容量。

最长公共子序列：找出一组序列的最长公共子序列（可由另一序列删除元素但不改变余下元素的顺序而得到）。

矩阵链相乘：给出一系列矩阵，目标是找到这些矩阵相乘的最高效办法（计算次数尽可能少）。相乘运算不会进行，解决方案是找到这些矩阵各自相乘的顺序。

硬币找零：给出面额为 *d*1, …, *d**n*的一定数量的硬币和要找零的钱数，找出有多少种找零的方法。

图的全源最短路径：对所有顶点对(*u*, *v*)，找出从顶点 *u* 到顶点 *v* 的最短路径。我们在第 9章已经学习过这个问题的 Floyd-Warshall 算法。

## 最少硬币找零问题

最少硬币找零问题是硬币找零问题的一个变种。硬币找零问题是给出要找零的钱数，以及可用的硬币面额 *d*1, …, *d**n*及其数量，找出有多少种找零方法。最少硬币找零问题是给出要找零的钱数，以及可用的硬币面额 *d*1, …, *d**n*及其数量，找到所需的最少的硬币个数。

例如，美国有以下面额（硬币）：*d*1 = 1，*d*2 = 5，*d*3 = 10，*d*4 = 25。

如果要找 36 美分的零钱，我们可以用 1 个 25 美分、1 个 10 美分和 1 个便士（1 美分）。

如何将这个解答转化成算法？

最少硬币找零的解决方案是找到 *n* 所需的最小硬币数。但要做到这一点，首先得找到对每个

*x* < *n* 的解。然后，我们可以基于更小的值的解来求解

```js
function minCoinChange(coins, amount) {
  const cache = [];
  const makeChange = (value) => {
    if (!value) {
      return [];
    }
    if (cahce[value]) {
      return cache[value];
    }

    let min = [];
    let newMin;
    let newAmount;
    for (let i = 0; i < coins.length; i++) {
      const coin = coins[i];
      newAmount = value - coin;
      if (newAmount >= 0) {
        newMin = makeChange(newAmount);
      }
      if (
        newAmount >= 0 &&
        (newMin.length < min.length - 1 || !min.length) &&
        (newMin.length || !newAmount)
      ) {
        min = [coin].concat(newMin);
        console.log("new Min " + min + " for " + amount);
      }
    }
    return (cahce[value] = min);
  };
  return makeChange(amount);
}

```

minCoinChange 参数接收 coins 参数，该参数代表问题中的面额。对美国的硬币系统而言，它是[1, 5, 10, 25]。我们可以随心所欲地传递任何面额。此外，为了更加高效且不重复计算值，我们使用了 cache（这个技巧称为记忆化）。接下来是 minCoinChange 函数中的 makeChange 方法，它也是一个递归函数，用来解决问题。makeChange 函数在行{13}被调用，amount 作为参数传入。由于 makeChange是一个内部函数，它也能访问到 cache 变量。

为了进一步帮助我们，我们基于 coins 参数（面额）解决问题。因此，对每个面额，我们都计算 newAmount的值，它的值会一直减小，直到能找零的最小钱数（别忘了本算法对所有的 x < amount 都会计算 makeChange 结果）。若 newAmount 是合理的值（正值），

我们也会计算它的找零结果。最后，我们判断 newAmount 是否有效，minValue （最少硬币数）是否是最优解，与此同时 minValue 和 newAmount 是否是合理的值。若以上判断都成立，意味着有一个比之前更优的答案（——以 5 美分为例，可以给 5 便士或者 1 个 5 美分镍币，1 个 5 美分镍币是最优解）。最后，返回最终结果。

## 背包问题

背包问题是一个组合优化问题。它可以描述如下：给定一个固定大小、能够携重量 *W* 的背包，以及一组有价值和重量的物品，找出一个最佳解决方案，使得装入背包的物品总重量不超过*W*，且总价值最大

下面是一个例子。

| 物品 | 重量 | 价值 |
| ---- | ---- | ---- |
| 1    | 2    | 3    |
| 2    | 3    | 4    |
| 3    | 4    | 5    |

考虑背包能够携带的重量只有 5。对于这个例子，我们可以说最佳解决方案是往背包里装入物品 1 和物品 2。这样，总重量为 5，总价值为 7

这个问题有两个版本。**0-1** 版本只能往背包里装完整的物品，而**分数背包问题**则允许装入分数物品。在这个例子里，我们将处理该问题的 0-1 版本。动态规划对分数版本无能为力，但本章稍后要学习的贪心算法可以解决它。

```js
// 0-1背包问题
function knapSack(capacity, weights, values, n) {
  const KS = [];
  for (let i = 0; i <= n; i++) {
    //{1}
    KS[i] = [];
  }
  for (let i = 0; i <= n; i++) {
    for (let w = 0; w <= capacity; w++) {
      if (i === 0 || w === 0) {
        //{2}
        KS[i][w] = 0;
      } else if (weights[i - 1] <= w) {
        //{3}
        const a = values[i - 1] + KS[i - 1][w - weights[i - 1]];
        const b = KS[i - 1][w];
        KS[i][w] = a > b ? a : b; // {4} max(a,b)
      } else {
        KS[i][w] = KS[i - 1][w]; // {5}
      }
    }
  }
  findValues(n, capacity, KS, weights, values); // {6} 增加的代码
  return KS[n][capacity]; // {7}
}

```

我们可以用开头的例子来测试这个算法。

```
const values = [3,4,5], 

weights = [2,3,4], 

capacity = 5, 

n = values.length; 

console.log(knapSack(capacity, weights, values, n)); // 输出 7
```

请注意，这个算法只输出背包携带物品价值的最大值，而不列出实际的物品。我们可以增加下面的附加函数来找出构成解决方案的物品。

```js
function findValues(n, capacity, KS, weights, values) {
  let i = n;
  let k = capacity;
  console.log("构成解的物品：");
  while (i > 0 && k > 0) {
    if (KS[i][k] !== KS[i - 1][k]) {
      console.log(
        `物品 ${i} 可以是解的一部分 w,v: ${weights[i - 1]}, ${values[i - 1]}`
      );
      i--;
      k -= LS[i][k];
    } else {
      i--;
    }
  }
}
```

我们可以在 knapSack 函数的行{7}之前调用这个函数（在行{6}声明）。执行完整的算法，会得到如下输出。

**构成解的物品：**

**物品** **2** **可以是解的一部分** **w,v: 3,4** 

**物品** **1** **可以是解的一部分** **w,v: 2,3** 

**总价值****: 7** 

背包问题也可以写成递归形式。你可以在本书的源代码包中找到它的递归版本

## 最长公共子序列

另一个经常被当作编程挑战问题的动态规划问题是最长公共子序列（LCS）：找出两个字符串序列的最长子序列的长度。最长子序列是指，在两个字符串序列中以相同顺序出现，但不要求连续（非字符串子串）的字符串序列。

```js
//最长公共子序列
function lcs(wordX, wordY) {
  const m = wordX.length;
  const n = wordY.length;
  const l = [];

  for (let i = 0; i <= m; i++) {
    l[i] = []; // {1}
    for (let j = 0; i <= n; j++) {
      l[i][j] = 0; // {2}
    }
  }

  for (let i = 0; i <= m; i++) {
    for (let j = 0; j <= n; j++) {
      if (i === 0 || j === 0) {
        l[i][i] = 0;
      } else if (wordX[i - 1] === wordY[j - 1]) {
        l[i][j] = l[i - 1][j - 1] + 1; // {3}
      } else {
        const a = l[i - 1][j];
        const b = l[i][j - 1];
        l[i][j] = a > b ? a : b; // {4} max(a,b)
      }
    }
  }
  return l[m][n]; // {5}
}

```

如果比较背包问题和 LCS 算法，我们会发现两者非常相似。这项从顶部开始构建解决方案的技术被称为记忆化，而解决方案就在表格或矩阵的右下角。

像背包问题算法一样，这种方法只输出 LCS 的长度，而不包含 LCS 的实际结果。要提取这个信息，需要对算法稍作修改，声明一个新的 solution 矩阵

注意，代码中有一些注释，我们需要用以下代码替换这些注释。

 行{1}：solution[i] = [];

 行{2}：solution[i][j] = '0';

 行{3}：solution[i][j] = 'diagonal';

 行{4}：solution[i][j]=(l[i][j] == l[i-1][j]) ? 'top' : 'left';

 行{5}：printSolution(solution, wordX, m, n)

```js
function printSolution(solution, wordX, m, n) {
  let a = m;
  let b = n;
  let x = solution[a][b];
  let answer = "";
  while (x !== "0") {
    if (solution[a][b] === "diagonal") {
      answer = wordX[a - 1] + answer;
      a--;
      b--;
    } else if (solution[a][b] === "left") {
      b--;
    } else if (solution[a][b] === "top") {
      a--;
    }
    x = solution[a][b];
  }
  console.log("lcs: " + answer);
}
```



```js
function lcs2(wordX, wordY) {
  const m = wordX.length;
  const n = wordY.length;
  const solution = [];
  for (let i = 0; i <= m; i++) {
    solution[i] = []; // {1}
    for (let j = 0; i <= n; j++) {
      solution[i][j] = "0"; // {2}
    }
  }

  for (let i = 0; i <= m; i++) {
    for (let j = 0; j <= n; j++) {
      if (i === 0 || j === 0) {
        solution[i][i] = 0;
      } else if (wordX[i - 1] === wordY[j - 1]) {
        solution[i][j] = "diagonal"; // {3}
      } else {
        solution[i][j] = solution[i][j] == solution[i - 1][j] ? "top" : "left"; // {4} max(a,b)
      }
    }
  }
  return printSolution(solution, wordX, m, n); // {5}
}

```

LCS 问题也可以写成递归形式。你可以在本书的源代码包中找到它的递归版本