---
title: 学习JavaScript数据结构与算法（第3版）第十二章 图
date: 2022-11-22 07:19:59
tags:
- [数据结构]
categories:
- [数据结构]
---







```js
import Dictionary from "../Dictinoary/Dictionary.js";
import { Colors, initialzecolor } from "../util.js";
import Queue from "../Queue/Queue.js";
export default class Graph {
  constructor(isDirected = false) {
    //判断是否是有向还是无向
    this.isDirected = isDirected;
    this.vertices = [];
    //用字典进行映射
    this.adjList = new Dictionary();
  }
  /**
   * 用来向图中添加一个新的顶点（因为图实例化后是空的）
   * @param {*} v
   */
  addVertex(v) {
    if (!this.vertices.includes(v)) {
      this.vertices.push(v);
      this.adjList.set(v, []);
    }
  }
  /**
   * 添加对应的边
   * @param {*} v
   * @param {*} w
   */
  addEdge(v, w) {
    if (!this.adjList.get(v)) {
      this.addVertex(v);
    }
    if (!this.adjList.get(w)) {
      this.addVertex(w);
    }
    !this.adjList.get(v).includes(w) && this.adjList.get(v).push(w);
    if (!this.isDirected) {
      !this.adjList.get(w).includes(v) && this.adjList.get(w).push(v);
    }
  }

  getVertices() {
    return this.vertices;
  }

  getAdjList() {
    return this.adjList;
  }

  toString() {
    let s = "";
    for (let i = 0; i < this.vertices.length; i++) {
      s += `${this.vertices[i]} -> `;
      const neighbors = this.adjList.get(this.vertices[i]);
      for (let j = 0; j < neighbors.length; j++) {
        s += `${neighbors[j]} `;
      }
      s += "\n"; // {18}
    }
    return s;
  }
}

/**
 * 广度优先搜索
 */

export const breadthFirstSearch = (graph, startVertex, callback) => {
  //获取图的结点
  const vertices = graph.getVertices();
  //获取图的结点映射
  const adjList = graph.getAdjList();
  //初始化
  const color = initialzecolor(vertices);
  //初始化队列
  const queue = new Queue();
  //入队
  queue.enqueue(startVertex);
  //循环队列
  while (!queue.isEmpty()) {
    //出队
    const u = queue.dequeue();
    //获取结点对应的映射
    const neighbors = adjList.get(u);
    //设置访问过的结点
    color[u] = Colors.GREY;
    //for循环映射
    for (let i = 0; i < neighbors.length; i++) {
      const w = neighbors[i];
      //如果该结点没有被访问过
      if (color[w] === Colors.WHITE) {
        //设置为已访问过
        color[w] = Colors.GREY;
        //入队
        queue.enqueue(w);
      }
    }
    //当该相关的映射全部入对即已访问过了，则标记
    color[u] = Colors.BLACK;
    //执行回调函数
    if (callback) {
      callback(u);
    }
  }
};

/**
 * 使用 BFS 寻找最短路径
 */

/**
 * 给定一个图 G 和源顶点 v，找出每个顶点 u 和 v 之间最短路径的距离（以边的数量计）。
对于给定顶点 v，广度优先算法会访问所有与其距离为 1 的顶点，接着是距离为 2 的顶点，
以此类推。所以，可以用广度优先算法来解这个问题。我们可以修改 breadthFirstSearch 方
法以返回给我们一些信息：
 从 v 到 u 的距离 distances[u]；
 前溯点 predecessors[u]，用来推导出从 v 到其他每个顶点 u 的最短路径。
 */

export const BFS = (graph, startVertex) => {
  //获得所有的结点
  const vertices = graph.getVertices();

  //获得所有结点对应的映射
  const adjList = graph.getAdjList();

  //初始化数据
  const color = initialzecolor(vertices);
  //初始化队列
  const queue = new Queue();

  //定义起始结点到各个结点的距离
  const distances = {};
  //每个结点的前导结点
  const predecessors = {};
  //起始结点入队
  queue.enqueue(startVertex);
  //初始化
  for (let i = 0; i < vertices.length; i++) {
    distances[vertices[i]] = 0;
    predecessors[vertices[i]] = null;
  }
  //遍历队列
  while (!queue.isEmpty()) {
    const u = queue.dequeue();

    const neighbors = adjList.get(u);

    color[u] = Colors.GREY;
    for (let i = 0; i < neighbors.length; i++) {
      const w = neighbors[i];
      if (color[w] === Colors.WHITE) {
        color[w] = Colors.GREY;
        distances[w] = distances[u] + 1;
        predecessors[w] = u;
        queue.enqueue(w);
      }
    }

    color[u] = Colors.BLACK;
  }

  return {
    distances,
    predecessors,
  };
};

//深度优先遍历算法
export const depthFirstSearch = (graph, callback) => {
  const vertices = graph.getVertices();
  const adjList = graph.getAdjList();
  const color = initialzecolor(vertices);
  for (let i = 0; i < vertices.length; i++) {
    if (color[vertices[i]] === Colors.WHITE) {
      depthFirstSearchVisit(vertices[i], color, adjList, callback);
    }
  }
};

const depthFirstSearchVisit = (u, color, adjList, callback) => {
  color[u] = Colors.GREY;
  if (callback) {
    callback(u);
  }
  const neighbors = adjList.get(u);

  for (let i = 0; i < neighbors.length; i++) {
    const w = neighbors[i];
    if (color[w] === Colors.WHITE) {
      depthFirstSearchVisit(w, color, adjList, callback);
    }
  }
  color[u] = Colors.BLACK;
};

/**
 * 我们可以用该算法做更多的事情，而不只是输出被访问顶点的顺序。对于给定的图 G，我们希望深度优先搜索算法遍历图 G 的所有节点，构建“森林”（有根树
的一个集合）以及一组源顶点（根），并输出两个数组：发现时间和完成探索时间。我们可以修改 depthFirstSearch 函数来返回一些信息：
 顶点 u 的发现时间 d[u]；
 当顶点 u 被标注为黑色时，u 的完成探索时间 f[u]； 
 顶点 u 的前溯点 p[u]。
 */

export const DFS = (graph, callback) => {
  const vertices = graph.getVertices();
  const adjList = graph.getAdjList();
  const color = initialzecolor(vertices);

  //结点的发现时间
  const d = {};
  //子结点遍历玩的时间
  const f = {};
  //前置结点
  const p = {};
  //计时
  const time = { count: 0 };
  //初始化每个结点
  for (let i = 0; i < vertices.length; i++) {
    d[vertices[i]] = 0;
    f[vertices[i]] = 0;
    p[vertices[i]] = null;
  }

  for (let i = 0; i < vertices.length; i++) {
    if (color[vertices[i]] === Colors.WHITE) {
      DFSVisit(vertices[i], color, d, f, p, time, adjList);
    }
  }

  return {
    discovery: d,
    finished: f,
    predecessors: p,
  };
};

const DFSVisit = (u, color, d, f, p, time, adjList) => {
  color[u] = Colors.GREY;
  d[u] = ++time.count;
  const neighbors = adjList.get(u);
  for (let i = 0; i < neighbors.length; i++) {
    const w = neighbors[i];
    if (color[w] === Colors.WHITE) {
      p[w] = u;
      DFSVisit(w, color, d, f, p, time, adjList);
    }
  }
  color[u] = Colors.BLACK;
  f[u] = ++time.count;
};
/**
 * 对于改进过的深度优先搜索，有两点需要我们注意
 * 时间（time）变量值的范围只可能在图顶点数量的一倍到两倍（2|V|）之间；
 * 对于所有的顶点 u，d[u]<f[u]（意味着，发现时间的值比完成时间的值小，完成时间意思是所有顶点都已经被探索过了）
 * 在这两个假设下，我们有如下的规则。1 <= d [u] < f [u] <= 2|V|
 */

/**
 * 拓扑排序——使用深度优先搜索
 */
```

