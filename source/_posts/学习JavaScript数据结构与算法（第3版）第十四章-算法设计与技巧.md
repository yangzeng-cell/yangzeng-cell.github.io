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

## 矩阵链相乘

矩阵链相乘是另一个可以用动态规划解决的著名问题。这个问题是要找出一组矩阵相乘的最佳方式（顺序）。

让我们试着更好地理解这个问题。*n* 行 *m* 列的矩阵 **A** 和 *m* 行 *p* 列的矩阵 **B** 相乘，结果是 *n* 行 *p* 列的矩阵 **C**。

考虑我们想做 **A*****B*****C*****D** 的乘法。因为乘法满足结合律，所以我们可以让这些矩阵以任意顺序相乘。因此，考虑如下情况：

 **A** 是一个 10 行 100 列的矩阵；

 **B** 是一个 100 行 5 列的矩阵；

 **C** 是一个 5 行 50 列的矩阵；

 **D** 是一个 50 行 1 列的矩阵；

 **A*****B*****C*****D** 的结果是一个 10 行 1 列的矩阵。

在这个例子里，相乘的方式有五种。

(1) (**A**(**B**(**CD**)))：乘法运算的次数是 1750 次。

(2) ((**AB**)(**CD**))：乘法运算的次数是 5300 次。

(3) (((**AB**)**C**)**D**)：乘法运算的次数是 8000 次。

(4) ((**A**(**BC**))**D**)：乘法运算的次数是 75 500 次。

(5) (**A**((**BC**)**D**))：乘法运算的次数是 31 000 次。

相乘的顺序不一样，要进行的乘法运算总数也有很大差异。那么，要如何构建一个算法，求出最少的乘法运算次数？矩阵链相乘的算法如下。

```js
//矩阵链相乘
function matrixChainOrder(p) {
  const n = p.length;
  const m = [];
  const s = [];
  for (let i = 1; i <= n; i++) {
    m[i] = [];
    m[i][i] = 0;
  }

  for (let l = 2; l < n; l++) {
    for (let i = 1; i <= n - l + 1; i++) {
      const j = i + l - 1;
      m[i][j] = Number.MAX_SAFE_INTEGER;
      for (let k = 0; k <= j - 1; k++) {
        const q = m[i][k] + m[k + 1][j] + p[i - 1] * p[k] * p[j]; //{1}
        if (q < m[i][j]) {
          m[i][j] = q; //{2}
        }
      }
    }
  }
  return m[1][n - 1];
}
```

整个算法中最重要的是行{1}，神奇之处全都在这一行。它计算了给定括号顺序的乘法运算次数，并将值保存在辅助矩阵 m 中。对开头的例子执行上面的算法，会得到结果 1750。正如我们前面提到的，这是最少的运算次数。看看下面的代码。

```
const p = [10, 100, 5, 50, 1]; 

console.log(matrixChainOrder(p)); 
```

然而，这个算法也不会给出最优解的括号顺序。为了得到这些信息，我们可以对代码做一些改动。

首先，需要通过以下代码声明并初始化一个辅助矩阵 s。

```js
const s = []; 
for (let i = 0; i <= n; i++){ 
 s[i] = []; 
 for (let j=0; j <= n; j++){ 
 s[i][j] = 0; 
 } 
}
```

然后，在 matrixChainOrder 函数的行{2}添加下面的代码。

```js
s[i][j] = k;
```

在行{3}，我们调用打印括号的函数，如下所示。

```
printOptimalParenthesis(s, 1, n-1); 
```

最后，我们的 printOptimalParenthesis 函数如

```js
function printOptimalParenthesis(s, i, j) {
  if (i === j) {
    console.log("A[" + i + "]");
  } else {
    console.log("(");
    printOptimalParenthesis(s, i, s[i][j]);
    printOptimalParenthesis(s, s[i][j] + 1, j);
    console.log(")");
  }
}
```

执行修改后的算法，也能得到括号的最佳顺序(A[1](A[2](A[3]A[4])))，并可以转化为(A(B(CD)))。

# 贪心算法

贪心算法遵循一种近似解决问题的技术，期盼通过每个阶段的局部最优选择（当前最好的解），从而达到全局的最优（全局最优解）。它不像动态规划算法那样计算更大的格局。我们来看看如何用贪心算法解决动态规划话题中最少硬币找零问题和背包问题

我们在第 12 章介绍了一些其他的贪心算法，比如 Dijkstra 算法、Prim 算法和Kruskal 算法。

用贪心算法解决动态规划话题中最少硬币找零问题和背包问题

## 最少硬币找零问题

最少硬币找零问题也能用贪心算法解决。大部分情况下的结果是最优的，不过对有些面额而言，结果不会是最优的。

```js
function minCoinChange(coins, amount) {
  const change = [];
  let total = 0;
  for (let i = coins.length; i >= 0; i--) {//{1}
    const coin = coins[i];
    while (total + coin <= amount) {//{2}
      change.push(coin);//{3}
      total += coin;//{4}
    }
  }
  return change;
}
```

不得不说贪心版本的 minCoinChange 比动态规划版本简单多了。对每个面额（行{1}——从大到小），把它的值和 total 相加后，total 需要小于 amount（行{2}）。我们会将当前面额coin 添加到结果中（行{3}），也会将它和 total 相加（行{4}）。

如你所见，这个贪心解法很简单。从最大面额的硬币开始，拿尽可能多的这种硬币找零。当无法再拿更多这种价值的硬币时，开始拿第二大价值的硬币，依次继续。

```
console.log(minCoinChange([1, 5, 10, 25], 36))
```

结果依然是[25, 10, 1]，和用 DP 得到的一样

然而，如果用[1, 3, 4]面额执行贪心算法，会得到结果[4, 1, 1]。如果用动态规划的解法，会得到最优的结果[3, 3]。

比起动态规划算法而言，贪心算法更简单、更快。然而，如我们所见，它并不总是得到最优答案。但是综合来看，它相对执行时间来说，输出了一个可以接受的解。

## 分数背包问题

求解分数背包问题的算法与动态规划版本稍有不同。在 0-1 背包问题中，只能向背包里装入完整的物品，而在分数背包问题中，可以装入分数的物品。我们用前面用过的例子来比较两者的差异，如下所示。

| 物品 | 重量 | 价值 |
| ---- | ---- | ---- |
| 1    | 2    | 3    |
| 2    | 3    | 4    |
| 3    | 4    | 5    |

在动态规划的例子里，我们考虑背包能够携带的重量只有 5。在这个例子里，我们可以说最佳解决方案是往背包里装入物品 1 和物品 2，总重量为 5，总价值为 7。

如果在分数背包问题中考虑相同的容量，得到的结果是一样的。因此，我们考虑容量为 6 的情况。

在这种情况下，解决方案是装入物品 1 和物品 2，还有 25%的物品 3。这样，重量为 6 的物品总价值为 8.25

```js
function knapSack(capacity, weights, values) {
  const n = values.length;
  let load = 0;
  let val = 0;
  for (let i = 0; i < n && load < capacity; i++) { // {1}
    if (weights[i] <= capacity - load) { // {2}
      val += values[i];
      load += weights[i];
    } else {
      const r = (capacity - load) / weights[i]; // {3}
      val += r * values[i];
      load += weights[i];
    }
  }
  return val;
}
```

总重量少于背包容量（不能带超过容量的东西），我们会迭代物品（行{1}）。如果物品可以完整地装入背包（行{2}——小于等于背包容量），就将其价值和重量分别计入背包已装入物品的总价值（val）和总重量（load）。如果物品不能完整地装入背包，计算能够装入部分的比例（r）（行{3}——我们可以带的分数）

如果在 0-1 背包问题中考虑同样的容量 6，我们就会看到，物品 1 和物品 3 组成了解决方案。在这种情况下，对同一个问题应用不同的解决方法，会得到两种不同的结果。

# 回溯算法

回溯是一种渐进式寻找并构建问题解决方式的策略。我们从一个可能的动作开始并试着用这个动作解决问题。如果不能解决，就回溯并选择另一个动作直到将问题解决。根据这种行为，回溯算法会尝试所有可能的动作（如果更快找到了解决办法就尝试较少的次数）来解决问题

有一些可用回溯解决的著名问题：

骑士巡逻问题

*N* 皇后问题

迷宫老鼠问题

数独解题器

## 迷宫老鼠问题

假设我们有一个大小为 *N* × *N* 的矩阵，矩阵的每个位置是一个方块。每个位置（或块）可以是空闲的（值为 1）或是被阻挡的（值为 0），如下图所示，其中 S 是起点，D 是终点

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2012.19.24.png)

矩阵就是迷宫，“老鼠”的目标是从位置[0][0]开始并移动到[n-1][n-1]（终点）。老鼠可以在垂直或水平方向上任何未被阻挡的位置间移动

```js
export function ratInAMaze(maze) {
  const solution = [];
  for (let i = 0; i < maze.length; i++) { //{1}
    solution[i] = [];
    for (let j = 0; j < maze[i].length; i++) { //{2}
      solution[i][j] = 0;
    }
  }
  if (findPath(maze, 0, 0, solution) === true) { //{3}
    return solution;
  }
  return "NO PATH FOUND";
}
```

首先创建一个包含解的矩阵。将每个位置初始化为零（行{1}）。对于老鼠采取的每步行动，我们将路径标记为 1。如果算法能够找到一个解（行{2}），就返回解决矩阵，否则返回一条错误信息（行{3}）。

然后，我们创建一个 findPath 方法，它会试着从位置 x 和 y 开始在给定的 maze 矩阵中找到一个解。和本章介绍的其他技巧一样，回溯技巧也使用了递归，这也是使这个算法有回溯能力的原因。

```js
function findPath(maze, x, y, solution) {
  const n = maze.length;
  if (x === n - 1 && y === n - 1) { //{4}
    solution[x][y] = 1;
    return true;
  }
  if (isSafe(maze, x, y) === true) { //{5}
    solution[x][y] = 1; //{6}
    if (findPath(maze, x + 1, y, solution)) { //{7}
      return true;
    }
    if (findPath(maze, x, y + 1, solution)) { //{8}
      return true;
    }
    solution[x][y] = 0; //{9}
    return false;
  }
  return false; //{10}
}
```

算法的第一步是验证老鼠是否到达了终点（行{4}）。如果到了，就将最后一个位置标记为路径的一部分并返回 true，表示移动成功结束。如果不是最后一步，要验证老鼠能否安全移动至该位置（行{5}表示根据下面声明的 isSafe 方法判断出该位置空闲）。如果是安全的，我们将这步加入路径（行{6}）并试着在 maze 矩阵中水平移动（向右）到下一个位置（行{7}）。如果水平移动不可行，我们就试着垂直向下移动到下一个位置（行{8}）。如果水平和垂直都不能移动，那么将这步从路径中移除并回溯（行{9}），表示算法会尝试另一个可能的解。在算法尝试了所有可能的动作还是找不到解时，就返回 false（行{10}），表示这个问题无解。

```js
 function isSafe(maze, x, y) {
 const n = maze.length;
  if (x >= 0 && y >= 0 && x < n && y < n && maze[x][y] !== 0) {
    return true;
  }
  return false;
}
```

用下面的代码进行测试。

```js
const maze = [ 
 [1, 0, 0, 0], 
 [1, 1, 1, 1], 
 [0, 0, 1, 0], 
 [0, 1, 1, 1] 
]; 
console.log(ratInAMaze(maze));
```

输出如下。

```js
 [1, 1, 1, 0], 
 [0, 0, 1, 0], 
 [0, 0, 1, 1]]
```

## 数独解题器

数独是一个非常有趣的解谜游戏，也是史上最流行的游戏之一。目标是用数字 1～9 填满一个 9 × 9 的矩阵，要求每行和每列都由这九个数字构成。矩阵还包含了小方块（3 × 3 矩阵），它们同样需要分别用这九个数字填满。谜题在开始给出一个已填了部分数字的矩阵，如下图所示

![](https://cdn.jsdelivr.net/gh/yangzeng-cell/blog-images/%E6%88%AA%E5%B1%8F2023-02-04%2016.22.47.png)

数独解题器的回溯算法会尝试在每行每列中填入每个数字。和迷宫老鼠问题一样，我们从算法的主方法开始。

```js
function sudokuSolver(matrix) { 
 if (solveSudoku(matrix) === true) { 
 return matrix; 
 } 
 return '问题无解！'; 
}
```

算法在找到解后会返回填满了缺失数字的矩阵，否则返回错误信息。现在，我们来看算法的主要逻辑。

```js
const UNASSIGNED = 0;
function solveSudoku(matrix) {
  let row = 0;
  let col = 0;
  let checkBlankSpaces = false; //是否空白
  for (row = 0; row < matrix.length; row++) { //{1}
    for (col = 0; col <= matrix[row].length; col++) {
      if (matrix[row][col] === UNASSIGNED) {
        checkBlankSpaces = true;  // {2}
        break;
      }
    }
    if (checkBlankSpaces === true) { // {3}
      break;
    }
  }
  if (checkBlankSpaces === false) {
    return true; // {4}
  }
  for (let num = 1; num <= 9; num++) { // {5}
    if (isSafe(matrix, row, col, num)) {// {6}
      matrix[row][col] = num; // {7}
      if (solveSudoku(matrix)) { // {8}
        return true;
      }
      matrix[row][col] = UNASSIGNED; // {9}
    }
  }
  return false; // {10}
}
```

第一步是验证谜题是否已被解决（行{1}）。如果没有空白的位置（值为 0 的位置），表示谜题已被完成（行{4}）。如果有空白位置（行{2}），我们要从两个循环中跳出（行{3}）并且 row和 col 变量会表示需要用 1～9 填写空白的位置。下面，算法会试着用 1～9 填写这个位置，一次填一个（行{5}）。我们会检查添加的数字是否符合规则（行{6}），也就是这个数字在这行、这列或在小矩阵（3 × 3 矩阵）中没有出现过。如果符合，我们就将这个数字填入（行{7}）并再次执行 solveSudoku 函数来尝试填写下一个位置（行{8}）。如果一个数字填在了不正确的位置，我们就再将这个位置标记为空（行{9}），并且算法会回溯（行{10}）再尝试一个其他数字

isSafe 声明如下，它包含检查填入的数字是否符合规则。

```js
function isSafe(matrix, row, col, num) {
  return (
    !usedInRow(matrix, row, num) &&
    !usedInCol(matrix, col, num) &&
    !usedInBox(matrix, row - (row % 3), col - (col % 3), num)
  );
}
```

具体的检查声明如下。

```js
function usedInRow(matrix, row, num) {
  for (let col = 0; col < matrix.length; col++) { // {11}
    if (matrix[row][col] === num) {
      return true;
    }
  }
  return false;
}
function usedInCol(matrix, col, num) {
  for (let row = 0; row < matrix.length; row++) { // {12}
    if (matrix[row][col] === num) {
      return true;
    }
  }
  return false;
}
function usedInBox(matrix, boxStartRow, boxStartCol, num) {
  for (let row = 0; row < 3; row++) {
    for (let col = 0; col < 3; col++) {
      if (matrix[row + boxStartRow][col + boxStartCol] === num) {
        return true;
      }
    }
  }
  return false;
}
```

首先，通过迭代矩阵中给定行 row 中的每个位置检查数字是否在行 row 中存在（行{11}）。然后，迭代所有的列来验证数字是否在给定的列中存在（行{12}）。最后的检查是通过迭代 3 × 3=矩阵中的所有位置来检查数字是否在小矩阵中存在（行{13}）

```
const sudokuGrid = [ 
 [5, 3, 0, 0, 7, 0, 0, 0, 0], 
 [6, 0, 0, 1, 9, 5, 0, 0, 0], 
 [0, 9, 8, 0, 0, 0, 0, 6, 0], 
 [8, 0, 0, 0, 6, 0, 0, 0, 3], 
 [4, 0, 0, 8, 0, 3, 0, 0, 1], 
 [7, 0, 0, 0, 2, 0, 0, 0, 6], 
 [0, 6, 0, 0, 0, 0, 2, 8, 0], 
 [0, 0, 0, 4, 1, 9, 0, 0, 5], 
 [0, 0, 0, 0, 8, 0, 0, 7, 9] 
]; 
console.log(sudokuSolver(sudokuGrid));
结果
[[5, 3, 4, 6, 7, 8, 9, 1, 2], 
 [6, 7, 2, 1, 9, 5, 3, 4, 8],
  [1, 9, 8, 3, 4, 2, 5, 6, 7], 
 [8, 5, 9, 7, 6, 1, 4, 2, 3], 
 [4, 2, 6, 8, 5, 3, 7, 9, 1], 
 [7, 1, 3, 9, 2, 4, 8, 5, 6], 
 [9, 6, 1, 5, 3, 7, 2, 8, 4], 
 [2, 8, 7, 4, 1, 9, 6, 3, 5], 
 [3, 4, 5, 2, 8, 6, 1, 7, 9]]
```

# 函数式编程简介

在命令式编程中，我们按部就班地编写程序代码，详细描述要完成的事情以及完成的顺序。

## 函数式编程与命令式编程

以函数式范式进行开发并不简单，关键在于习惯这种范式的机制。我们编写一个例子来说明差异。

假设我们想打印一个数组中所有的元素。我们可以用命令式编程，声明的函数如下

```js
const printArray = function(array) { 
 for (var i = 0; i < array.length; i++){ 
 console.log(array[i]); 
 } 
}; 
printArray([1, 2, 3, 4, 5]);
```

现在，我们试着把这个例子转换成函数式编程。在函数式编程中，函数就是摇滚明星。我们关注的重点是需要描述什么，而不是如何描述。回到这一句：“我们迭代数组，打印每一项。”那么，首先要关注的是迭代数据，然后进行操作，即打印数组项。下面的函数负责迭代数组。

```js
const forEach = function (array, action) {
  for (var i = 0; i < array.length; i++) {
    action(array[i]);
  }
};
```

接下来，要创建另一个负责把数组元素打印到控制台的函数（考虑为回调函数），如下所示

```js
const logItem = function (item) {
  console.log(item);
};
```

最后，像下面这样使用声明的函数。

```js
forEach([1, 2, 3, 4, 5], logItem); 
```

1. 函数式编程的主要目标是描述数据，以及要对数据应用的转换。
2. 在函数式编程中，程序执行顺序的重要性很低；而在命令式编程中，步骤和顺序是非常重要的。
3. 函数和数据集合是函数式编程的核心。
4. 在函数式编程中，我们可以使用和滥用函数和递归；而在命令式编程中，则使用循环、赋值、条件和函数。
5. 在函数式编程中，要避免副作用和可变数据，意味着我们不会修改传入函数的数据。如果需要基于输入返回一个解决方案，可以制作一个副本并返回数据修改后的副本

## ES2015+和函数式编程

有了 ES2015+的新功能，用 JavaScript 进行函数式编程就变得更加容易了

考虑我们要找出数组中最小的值。要用命令式编程完成这个任务，只要迭代数组，检查当前的最小值是否大于数组元素；如果是，就更新最小值，代码如下。

```js
var findMinArray = function(array){ 
 var minValue = array[0]; 
 for (var i=1; i<array.length; i++){ 
 if (minValue > array[i]){ 
 minValue = array[i]; 
 } 
 } 
 return minValue; 
}; 
console.log(findMinArray([8,6,4,5,9])); // 输出 4
```

要用函数式编程完成相同的任务，可以使用 Math.min 函数，传入所有要比较的数组元素。我们可以像下面的例子里这样，使用 ES2015 的解构运算符（...），把数组转换成单个的元素。

```js
const min_ = function(array){ 
 return Math.min(...array) 
}; 
console.log(min_([8,6,4,5,9])); // 输出 4
```

使用 ES2015 的箭头函数，可以进一步简化上面的代码

```js
const min = arr => Math.min(...arr); 
console.log(min([8, 6, 4, 5, 9]));
```

我们可以用 ES2015 语法重写第一个示例。

```js
const forEach = (array, action) => array.forEach(item => action(item)); 
const logItem = (item) => console.log(item);
```

## JavaScript 函数式工具箱——**map**、**filter** 和 **reduce**

我们可以使用 map 函数，把一个数据集合转换或映射成另一个数据集合。先看一个命令式编程的例子

```js
const daysOfWeek = [
  { name: "Monday", value: 1 },
  { name: "Tuesday", value: 2 },
  { name: "Wednesday", value: 7 },
];
let daysOfWeekValues_ = [];
for (let i = 0; i < daysOfWeek.length; i++) {
  daysOfWeekValues_.push(daysOfWeek[i].value);
}
```

再以函数式编程并使用 ES2015+语法来考虑同样的例子，代码如下

```js
const daysOfWeekValues = daysOfWeek.map(day => day.value); 
console.log(daysOfWeekValues);
```

我们可以使用 filter 函数过滤一个集合的值。下面来看一个例子。

```js
const positiveNumbers_ = function (array) {
  let positive = [];
  for (let i = 0; i < array.length; i++) {
    if (array[i] >= 0) {
      positive.push(array[i]);
    }
  }
  return positive;
};
console.log(positiveNumbers_([-1, 1, 2, -2]));
```

我们可以把同样的代码写成函数式的

```js
const positiveNumbers = (array) => array.filter(num => (num >= 0)); 
console.log(positiveNumbers([-1,1,2,-2]));
```

我们也可以使用 reduce 函数，把一个集合归约成一个特定的值。比如，对一个数组中的值求和

```js
const sumValues = function(array) { 
 let total = array[0]; 
 for (let i = 1; i<array.length; i++) { 
 total += array[i]; 
 } 
 return total; 
}; 
console.log(sumValues([1, 2, 3, 4, 5]));
```

上面的代码也可以写成这样

```js
const sum_ = function(array){ 
 return array.reduce(function(a, b){ 
 return a + b; 
 }) 
}; 
console.log(sum_([1, 2, 3, 4, 5]));
```

我们还可以把这些函数与 ES2015 的功能结合起来，比如解构运算符和箭头函数，代码如下

```js
const sum = arr => arr.reduce((a, b) => a + b); 
console.log(sum([1, 2, 3, 4, 5]));
```

我们再看另一个例子。考虑我们需要写一个函数，把几个数组连接起来。为此，可以创建另一个数组，用于存放其他数组的元素。可以执行以下命令式的代码。

```js
const mergeArrays_ = function(arrays){ 
 const count = arrays.length; 
 let newArray = []; 
 let k = 0; 
 for (let i = 0; i < count; i++){ 
 for (var j = 0; j < arrays[i].length; j++){ 
 newArray[k++] = arrays[i][j]; 
 } 
 } 
 return newArray; 
}; 
console.log(mergeArrays_([[1, 2, 3], [4, 5], [6]]));
```

注意，在这个例子中，我们声明了变量，还使用了循环。现在，我们用 JavaScript 函数式编程把上面的代码重写如下

```js
const mergeArraysConcat = function(arrays){ 
 return arrays.reduce( function(p,n){ 
 return p.concat(n); 
 }); 
}; 
console.log(mergeArraysConcat([[1, 2, 3], [4, 5], [6]]));
```

上面的代码完成了同样的任务，但它是面向函数的。我们也可以用 ES2015 使代码更加精简，如下所示。

```js
const mergeArrays = (...arrays) => [].concat(...arrays); 
console.log(mergeArrays([1, 2, 3], [4, 5], [6]));
```

