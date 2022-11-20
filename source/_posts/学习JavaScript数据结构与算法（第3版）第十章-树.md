---
title: 学习JavaScript数据结构与算法（第3版）第十章 树
date: 2022-11-20 22:19:27
tags:
- [数据结构]
categories:
- [数据结构]
---

# 树

```js
// 对两个数值进行浅层比较
//util.js
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

//

## 树的基本构造

```js
import { Compare, defaultCompare } from "../util.js";

export class Node {
  constructor(key) {
    this.key = key; //节点值
    this.left = null; //左侧子节点引用
    this.right = null; //右侧节点引用
  }
}

export default class BinarySearchTree {
  constructor(compareFn = defaultCompare) {
    this.compareFn = compareFn; //用来比较节点的值
    this.root = null; //node类型的根节点
  }
  /**
   * 向树中插入一个新的键
   * @param {*} key
   */
  insert(key) {
    if (this.root == null) {
      this.root = new Node(key);
    } else {
      this.insertNode(this.root, key);
    }
  }

  insertNode(node, key) {
    if (this.compareFn(key, node.key) === Compare.LESS_THAN) {
      if (node.left == null) {
        node.left = new Node(key);
      } else {
        this.insertNode(node.left, key);
      }
    } else {
      if (node.right == null) {
        node.right = new Node(key);
      } else {
        this.insertNode(node.right, key);
      }
    }
  }

  serachNode(node, key) {
    if (node == null) {
      return false;
    }

    if (this.compareFn(key, node.key) === Compare.LESS_THAN) {
      return this.serachNode(node.left, key);
    } else if (this.compareFn(key, node.key) === Compare.BIGGER_THAN) {
      return this.serachNode(node.right, key);
    } else {
      return true;
    }
  }

  /**
   * 在树中查找一个键。如果节点存在，则返回 true；如果不存在，则返回false
   * @param {*} key
   */
  search(key) {
    return this.serachNode(this.root, key);
  }

  inOrderTraverseNode(node, callback) {
    if (node != null) {
      this.inOrderTraverseNode(node.left, callback);
      callback(node.key);
      this.inOrderTraverseNode(node.right, callback);
    }
  }

  /**
   * 通过中序遍历方式遍历所有节点
   */
  inOrderTraverse(callback) {
    this.inOrderTraverseNode(this.root, callback);
  }

  preOrderTraverseNode(node, callback) {
    if (node != null) {
      callback(node.key);
      this.preOrderTraverseNode(node.left, callback);
      this.preOrderTraverseNode(node.right, callback);
    }
  }
  /**
   * 通过先序遍历方式遍历所有节点
   * preOrderTraverse
   */
  preOrderTraverse(callback) {
    this.preOrderTraverseNode(this.root, callback);
  }

  postOrderTraverseNode(node, callback) {
    if (node == null) return;
    this.postOrderTraverseNode(node.left, callback);
    this.postOrderTraverseNode(node.right, callback);
    callback(node.key);
  }

  /**
   * 通过后序遍历方式遍历所有节点
   */
  postOrderTraverse(callback) {
    this.postOrderTraverseNode(this.root, callback);
  }

  minNode(node) {
    let current = node;
    while (current != null && current.left != null) {
      current = current.left;
    }
    return current;
  }

  /**
   * 返回树中最小的值/键
   */
  min() {
    return this.minNode(this.root);
  }

  maxNode(node) {
    let current = node;
    while (current != null && current.right != null) {
      current = current.right;
    }
    return current;
  }
  /**
   * 返回树中最大的值/键
   */
  max() {
    return this.maxNode(this.root);
  }

  removeNode(node, key) {
    if (node == null) {
      return null;
    }

    if (this.compareFn(key, node.key) === Compare.LESS_THAN) {
      node.left = this.removeNode(node.left, key);
      return node;
    } else if (this.compareFn(key, node.key) === Compare.BIGGER_THAN) {
      node.right = this.removeNode(node.right, key);
      return node;
    } else {
      //第一种情况，是删除叶节点
      if (node.left == null && node.right == null) {
        node = null;
        return node;
        //第二种情况左子树为null
      } else if (node.left == null) {
        node = node.right;
        return node;
        //第三种情况右子树为null
      } else if (node.right == null) {
        node = node.left;
        return node;
        //第四种情况，左右子树都存在，需要找到右子树的自小节点或者左子树的最大节点来替换node
      } else {
        const aux = this.minNode(node.right);
        node.key = aux.key;
        node.right = this.removeNode(node.right, aux.key);
        return node;
      }
    }
  }

  /**
   * 从树中移除某个键
   * @param {*} key
   */
  remove(key) {
    this.root = this.removeNode(this.root, key);
  }
}

```

## AVL树

```js
import { Compare, defaultCompare } from "../util.js";
import BinarySearchTree from "./index.js";
import { Node } from "./index.js";
export const BalancerFactor = {
  UNBALANCED_RIGHT: 1,
  SLIGHTLY_UNBALANCED_RIGHT: 2,
  BALANCED: 3,
  SLIGHTLY_UNBALANCED_LEFT: 4,
  UNBALANCED_LEFT: 5,
};

//avl树就是自平衡二叉树，就是节点的左右子树的高度差不会超过1
export default class AVLTree extends BinarySearchTree {
  constructor(compareFn = defaultCompare) {
    super(compareFn);
  }

  insert(key) {
    this.root = this.insertNode(this.root, key);
  }

  insertNode(node, key) {
    //当这棵树是空树的时候
    if (node == null) {
      return new Node(key);
      //小于向前节点
    } else if (this.compareFn(key, node.key) === Compare.LESS_THAN) {
      node.left = this.insertNode(node.left, key);
      //大于当前节点
    } else if (this.compareFn(key, node.key) === Compare.BIGGER_THAN) {
      node.right = this.insertNode(node.right, key);
    } else {
      //和当前节点值相同时
      return node;
    }

    //判断是否平衡
    const balancerFactor = this.getBalanceFactor(node);

    if (balancerFactor === BalancerFactor.UNBALANCED_LEFT) {
      if (this.compareFn(key, node.left.key) === Compare.LESS_THAN) {
        node = this.rotationLL(node);
      } else {
        node = this.rotationLR(node);
      }
    }

    if (balancerFactor === BalancerFactor.UNBALANCED_RIGHT) {
      if (this.compareFn(key, node.right.key) === Compare.BIGGER_THAN) {
        node = this.rotationRR(node);
      } else {
        node = this.rotationRL(node);
      }
    }

    return node;
  }

  removeNode(node, key) {
    node = super.removeNode(node, key);

    if (node == null) {
      //空树也是平衡树
      return node;
    }

    const balancerFactor = this.getBalanceFactor(node);
    if (balancerFactor === BalancerFactor.UNBALANCED_LEFT) {
      const balancerFactorLeft = this.getBalanceFactor(node.left);
      if (
        balancerFactorLeft === BalancerFactor.UNBALANCED_LEFT ||
        balancerFactorLeft === BalancerFactor.SLIGHTLY_UNBALANCED_LEFT
      ) {
        return this.rotationLL(node);
      }
      if (balancerFactorLeft === BalancerFactor.SLIGHTLY_UNBALANCED_RIGHT) {
        return this.rotationLR(node.left);
      }
    }

    if (balancerFactor === BalancerFactor.UNBALANCED_RIGHT) {
      const balancerFactorRight = this.getBalanceFactor(node.right);
      if (
        balancerFactorRight === BalancerFactor.UNBALANCED_RIGHT ||
        balancerFactorRight === BalancerFactor.SLIGHTLY_UNBALANCED_RIGHT
      ) {
        return this.rotationRR(node);
      }

      if (balancerFactorRight === BalancerFactor.SLIGHTLY_UNBALANCED_LEFT) {
        return this.rotationRL(node.right);
      }
    }
    return node;
  }

  // 计算一个节点高度
  getNodeHeight(node) {
    if (node == null) {
      return -1;
    }

    return Math.max(
      this.getNodeHeight(node.left),
      this.getNodeHeight(node.right)
    );
  }

  // 计算一个节点的平衡因子并返回其值
  getBalanceFactor(node) {
    const heightDifference =
      this.getNodeHeight(node.left) - this.getNodeHeight(node.right);
    switch (heightDifference) {
      case -2:
        break;

      default:
        break;
    }
  }

  // 左左（LL）：向右的单旋转
  rotationLL(node) {
    const temp = node.left;
    node.right = temp.left;
    temp.right = node;

    return temp;
  }

  rotationRR(node) {
    const temp = node.right;
    node.left = temp.right;
    temp.left = node;
    return temp;
  }

  rotationLR(node) {
    node.left = this.rotationRR(node.left);
    return this.rotationLL(node);
  }

  rotationRL(node) {
    node.right = this.rotationLL(node.right);
    return this.rotationRR(node);
  }
}

```

## 红黑树

```js
// 在红黑树中，每个节点都遵循以下规则：
// (1) 顾名思义，每个节点不是红的就是黑的；
// (2) 树的根节点是黑的；
// (3) 所有叶节点都是黑的（用 NULL 引用表示的节点）；
// (4) 如果一个节点是红的，那么它的两个子节点都是黑的；
// (5) 不能有两个相邻的红节点，一个红节点不能有红的父节点或子节点；
// (6) 从给定的节点到它的后代节点（NULL 叶节点）的所有路径包含相同数量的黑色节点

import { Compare, defaultCompare } from "../util.js";
import BinarySearchTree, { Node } from "./index.js";

class RedBlackNode extends Node {
  constructor(key) {
    super(key);
    this.key = key;
    this.color = Colors.RED;
    this.parent = null;
  }
}

export const Colors = {
  RED: 1,
  BLACK: 2,
};
export default class RedBlackTree extends BinarySearchTree {
  constructor(compareFn = defaultCompare) {
    super(compareFn);
    this.compareFn = compareFn;
    this.root = null;
  }

  isRed() {
    return this.color === Colors.isRed;
  }
  insert(key) {
    this.root = this.insertNode(this.root, key);
  }

  insertNode(node, key) {
    if (this.compareFn(key, node.key) === Compare.LESS_THAN) {
      if (node.left == null) {
        node.left = new RedBlackNode(key);
        node.left.parent = node;
        return node.left;
      } else {
        return this.insertNode(node.left, key);
      }
    }

    if (this.compareFn(key, node.key) === Compare.BIGGER_THAN) {
      if (node.right == null) {
        node.right = new RedBlackNode(key);
        node.right.parent = node;
        return node.right;
      } else {
        return this.insertNode(node.right, key);
      }
    }
  }

  fixTreeProperties(node) {
    while (
      node &&
      node.parent &&
      node.parent.color.isRed() &&
      node.color !== Colors.BLACK
    ) {
      let parent = node.parent;
      const grendparent = parent.parent;
      // 情形 A1：父节点是左侧子节点
      if (grendparent && grendparent.left === parent) {
        const uncle = grendparent.right;
        if (uncle && uncle.color === Colors.RED) {
          grendparent.color = Colors.RED;
          uncle.color = Colors.BLACK;
          parent.color = Colors.BLACK;
          node = grendparent;
        } else {
          // 情形 2A：节点是右侧子节点——左旋转
          if (node === parent.right) {
            this.rotationRR(parent);
            node = parent;
            parent = node.parent;
          }
          // 情形 3A：节点是左侧子节点——右旋转
          this.rotationLL(grendparent);
          parent.color = Colors.BLACK;
          grendparent.color = Colors.RED;
          node = parent;
        }
      } else {
        const uncle = grendparent.left;
        if (uncle && uncle.color === Colors.RED) {
          grendparent.color = Colors.RED;
          uncle.color = Colors.BLACK;
          parent.color = Colors.BLACK;
          node = grendparent;
        } else {
          // 情形 2B：节点是左侧子节点——右旋转
          if (node === parent.left) {
            this.rotationLL(parent);
            node = parent;
            parent = node.parent;
          }
          // 情形 3B：节点是右侧子节点——左旋转
          this.rotationRR(grendparent);
          parent.color = Colors.BLACK;
          grendparent.color = Colors.RED;
          node = parent;
        }
      }
    }
    this.root.color = Colors.BLACK;
  }

  rotationLL(node) {
    const temp = node.left;
    node.left = temp.right;
    if (temp.right && temp.right.key) {
      temp.right.parent = node;
    }
    temp.parent = node.parent;
    if (!node.parent) {
      this.root = temp;
    } else {
      if (node === node.parent.left) {
        node.parent.left = temp;
      } else {
        node.parent.right = temp;
      }
    }
    temp.right = node;
    node.parent = temp;
  }

  rotationRR(node) {
    const temp = node.right;
    node.right = temp.left;
    if (temp.left && temp.left.key) {
      temp.left.parent = node;
    }
    temp.parent = node.parent;
    if (!node.parent) {
      this.root = temp;
    } else {
      if (node === node.parent.left) {
        node.parent.left = temp;
      } else {
        node.parent.right = temp;
      }
    }
    temp.right = node;
    node.parent = temp;
  }
}
```

