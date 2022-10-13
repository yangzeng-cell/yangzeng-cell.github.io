---
title: Promise的实现
date: 2022-09-05 09:38:12
tags:
categories:
- [javascript, 搜集]
---









#### 结构设计



```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        this.status = PROMISE_STATUS_FULFILLED;
        this.value = value;
        console.log("resolve被调用");
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        this.status = PROMISE_STATUS_REJECTED;
        this.reason = reason;
        console.log("reject被调用");
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
}

const promise = new myPromise((resolve, reject) => {
  console.log("padding");
  resolve(111);
  reject(222);
});

```



#### then方法的结构设计

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        this.status = PROMISE_STATUS_FULFILLED;
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          console.log("resolve");
          this.value = value;
          this.onFulfilled(this.value);
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        this.status = PROMISE_STATUS_REJECTED;
        queueMicrotask(() => {
          console.log("reject");
          this.reason = reason;
          this.onRejected(this.reason);
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    this.onFulfilled = onFulfilled;
    this.onRejected = onRejected;
  }
}

const promise = new myPromise((resolve, reject) => {
  console.log("padding");
  resolve(111);
  reject(222);
});
// 此时不可以进行链式调用，否则会报错
promise.then(
  (res) => {
    console.log("res1:", res);
    return 1111;
  },
  (err) => {
    console.log("err:", err);
  }
);

```



#### then方法的结构设计-优化

##### 解决then在状态确定之后调用的问题

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          console.log("resolve");
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          console.log("reject");
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    //当调用then的时候，状态已经确定
    if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
      onFulfilled(this.value);
    }

    if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
      onRejected(this.reason);
    }
    // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
    if (this.status === PROMISE_STATUS_PENDING) {
      this.onFulfilledFns.push(onFulfilled);
      this.onRejectedFns.push(onRejected);
    }
  }
}

const promise = new myPromise((resolve, reject) => {
  console.log("padding");
  resolve(111);
  reject(222);
});
// 调用then方法多次调用
promise.then(
  (res) => {
    console.log("res1:", res);
  },
  (err) => {
    console.log("err:", err);
  }
);

promise.then(
  (res) => {
    console.log("res2:", res);
  },
  (err) => {
    console.log("err2:", err);
  }
);

// const promise = new Promise((resolve, reject) => {
//   resolve("aaaaa")
// })

// 在确定Promise状态之后, 再次调用then
setTimeout(() => {
  promise.then(
    (res) => {
      console.log("res3:", res);
    },
    (err) => {
      console.log("err3:", err);
    }
  );
}, 1000);

```

#### then方法的结构设计-优化二

##### 解决then方法链式调用

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          console.log("resolve");
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          console.log("reject");
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    // 返回一个promise,就可以进行链式调用了
    return new myPromise((resolve, reject) => {
      //当调用then的时候，状态已经确定
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value);
        //   resolve(value);
        // } catch (error) {
        //   reject(error);
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }

      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason);
        //   resolve(reason);
        // } catch (error) {
        //   reject(error);
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
      if (this.status === PROMISE_STATUS_PENDING) {
        this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value);
          //   resolve(value);
          // } catch (error) {
          //   reject(error);
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
        });
        this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason);
          //   resolve(reason);
          // } catch (error) {
          //   reject(error);
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
        });
      }
    });
  }
}

const promise = new myPromise((resolve, reject) => {
  console.log("padding");
  resolve(111);
  reject(222);
});
// 调用then方法多次调用
promise
  .then(
    (res) => {
      console.log("res1:", res);
      return "aaaa";
      // throw new Error("err message")
    },
    (err) => {
      console.log("err1:", err);
      return "bbbbb";
      // throw new Error("err message")
    }
  )
  .then(
    (res) => {
      console.log("res2:", res);
    },
    (err) => {
      console.log("err2:", err);
    }
  );

```

#### catch的设计方法

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    // 将error传递下去交给catch处理
    const defaultOnRejected = (err) => {
      throw err;
    };
    onRejected = onRejected || defaultOnRejected;
    // 返回一个promise,就可以进行链式调用了
    return new myPromise((resolve, reject) => {
      //当调用then的时候，状态已经确定
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }

      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
      if (this.status === PROMISE_STATUS_PENDING) {
        if (onFulfilled) {
          this.onFulfilledFns.push(() => {
            execFunctionWithCatchError(
              onFulfilled,
              this.value,
              resolve,
              reject
            );
          });
        }
        if (onRejected) {
          this.onRejectedFns.push(() => {
            execFunctionWithCatchError(
              onRejected,
              this.reason,
              resolve,
              reject
            );
          });
        }
      }
    });
  }
  catch(onRejected) {
    this.then(undefined, onRejected);
  }
}

const promise = new myPromise((resolve, reject) => {
  console.log("状态pending");
  // resolve(1111) // resolved/fulfilled
  reject(2222);
});

// 调用then方法多次调用
promise
  .then((res) => {
    console.log("res:", res);
  })
  .catch((err) => {
    console.log("err:", err);
  });

```

#### finally方法的设计

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    // 将error传递下去交给catch处理
    const defaultOnRejected = (err) => {
      throw err;
    };
    const defaultOnFulfilled = (value) => {
      return value;
    };
    onRejected = onRejected || defaultOnRejected;

    onFulfilled = onFulfilled || defaultOnFulfilled;
    // 返回一个promise,就可以进行链式调用了
    return new myPromise((resolve, reject) => {
      //当调用then的时候，状态已经确定
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }

      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
      if (this.status === PROMISE_STATUS_PENDING) {
        if (onFulfilled) {
          this.onFulfilledFns.push(() => {
            execFunctionWithCatchError(
              onFulfilled,
              this.value,
              resolve,
              reject
            );
          });
        }
        if (onRejected) {
          this.onRejectedFns.push(() => {
            execFunctionWithCatchError(
              onRejected,
              this.reason,
              resolve,
              reject
            );
          });
        }
      }
    });
  }
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  finally(onfinally) {
    this.then(
      () => {
        onfinally();
      },
      () => {
        onfinally();
      }
    );
  }
}

const promise = new myPromise((resolve, reject) => {
  console.log("状态pending");
  resolve(1111); // resolved/fulfilled
  // reject(2222);
});

// 调用then方法多次调用
promise
  .then((res) => {
    console.log("res1:", res);
    return "aaaaa";
  })
  .then((res) => {
    console.log("res2:", res);
  })
  .catch((err) => {
    console.log("err:", err);
  })
  .finally(() => {
    console.log("finally");
  });

```

#### promise的类方法resolve/reject

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    // 将error传递下去交给catch处理
    const defaultOnRejected = (err) => {
      throw err;
    };
    const defaultOnFulfilled = (value) => {
      return value;
    };
    onRejected = onRejected || defaultOnRejected;

    onFulfilled = onFulfilled || defaultOnFulfilled;
    // 返回一个promise,就可以进行链式调用了
    return new myPromise((resolve, reject) => {
      //当调用then的时候，状态已经确定
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }

      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
      if (this.status === PROMISE_STATUS_PENDING) {
        if (onFulfilled) {
          this.onFulfilledFns.push(() => {
            execFunctionWithCatchError(
              onFulfilled,
              this.value,
              resolve,
              reject
            );
          });
        }
        if (onRejected) {
          this.onRejectedFns.push(() => {
            execFunctionWithCatchError(
              onRejected,
              this.reason,
              resolve,
              reject
            );
          });
        }
      }
    });
  }
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  finally(onfinally) {
    this.then(
      () => {
        onfinally();
      },
      () => {
        onfinally();
      }
    );
  }
  // 就是将value转成fulfilled状态可以进行then调用
  static resolve(value) {
    return new myPromise((resolve) => {
      return resolve(value);
    });
  }
  // 将reason装成rejected状态
  static reject(reason) {
    return new myPromise((resolve, reject) => {
      return reject(reason);
    });
  }
}

myPromise.resolve("Hello World").then((res) => {
  console.log("res:", res);
});

myPromise.reject("Error Message").catch((err) => {
  console.log("err:", err);
});
```

#### promise的类方法all/allSettled

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    // 将error传递下去交给catch处理
    const defaultOnRejected = (err) => {
      throw err;
    };
    const defaultOnFulfilled = (value) => {
      return value;
    };
    onRejected = onRejected || defaultOnRejected;

    onFulfilled = onFulfilled || defaultOnFulfilled;
    // 返回一个promise,就可以进行链式调用了
    return new myPromise((resolve, reject) => {
      //当调用then的时候，状态已经确定
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }

      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
      if (this.status === PROMISE_STATUS_PENDING) {
        if (onFulfilled) {
          this.onFulfilledFns.push(() => {
            execFunctionWithCatchError(
              onFulfilled,
              this.value,
              resolve,
              reject
            );
          });
        }
        if (onRejected) {
          this.onRejectedFns.push(() => {
            execFunctionWithCatchError(
              onRejected,
              this.reason,
              resolve,
              reject
            );
          });
        }
      }
    });
  }
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  finally(onfinally) {
    this.then(
      () => {
        onfinally();
      },
      () => {
        onfinally();
      }
    );
  }
  // 就是将value转成fulfilled状态可以进行then调用
  static resolve(value) {
    return new myPromise((resolve) => {
      return resolve(value);
    });
  }
  // 将reason装成rejected状态
  static reject(reason) {
    return new myPromise((resolve, reject) => {
      return reject(reason);
    });
  }
  // all传入一个promise数组，如果promise item状态都是fulfulled，all才是fuldilled状态，否则只要出现了reject就会变成renject
  static all(promises) {
    return new myPromise((resolve, reject) => {
      const values = [];
      promises.forEach((promise) => {
        promise.then(
          (res) => {
            values.push(res);
            if (values.length === promises.length) {
              resolve(values);
            }
          },
          (err) => {
            reject(err);
          }
        );
      });
    });
  }

  static allSettled(promises) {
    return new myPromise((resolve, reject) => {
      const values = [];
      promises.forEach((promise) => {
        promise.then(
          (res) => {
            values.push({
              status: PROMISE_STATUS_FULFILLED,
              value: res,
            });

            if (values.length === promises.length) {
              resolve(values);
            }
          },
          (err) => {
            values.push({ status: PROMISE_STATUS_REJECTED, value: err });
            if (values.length === promises.length) {
              resolve(values);
            }
          }
        );
      });
    });
  }
}

const p1 = new myPromise((resolve) => {
  setTimeout(() => {
    resolve(1111);
  }, 1000);
});
const p2 = new myPromise((resolve, reject) => {
  setTimeout(() => {
    reject(2222);
  }, 2000);
});
const p3 = new myPromise((resolve) => {
  setTimeout(() => {
    resolve(3333);
  }, 3000);
});
myPromise
  .all([p1, p2, p3])
  .then((res) => {
    console.log(res);
  })
  .catch((err) => {
    console.log(err);
  });

myPromise.allSettled([p1, p2, p3]).then((res) => {
  console.log(res);
});

```

#### promise类方法-any/race

```js
// 定义promise的三种状态
const PROMISE_STATUS_PENDING = "pending";
const PROMISE_STATUS_FULFILLED = "fulfilled";
const PROMISE_STATUS_REJECTED = "rejected";

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value);
    resolve(result);
  } catch (error) {
    reject(error);
  }
}

class myPromise {
  constructor(executor) {
    // 状态初始化
    this.status = PROMISE_STATUS_PENDING;
    // 定义resolve，reject的参数
    this.value = undefined;
    this.reason = undefined;
    this.onFulfilledFns = [];
    this.onRejectedFns = [];
    const resolve = (value) => {
      // 只有状态是padding的时候才可以进行改变，否则将不会有输出
      if (this.status === PROMISE_STATUS_PENDING) {
        // 将回调函数加入到微任务队列中，等调用then获取到onFulfilled函数的时候再调用执行
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_FULFILLED;
          this.value = value;
          this.onFulfilledFns.forEach((fn) => {
            fn(this.value);
          });
        });
      }
    };
    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return;
          this.status = PROMISE_STATUS_REJECTED;
          this.reason = reason;
          this.onRejectedFns.forEach((fn) => {
            fn(this.reason);
          });
        });
      }
    };
    try {
      executor(resolve, reject);
    } catch (error) {
      reject(error);
    }
  }
  // then方法的实现
  then(onFulfilled, onRejected) {
    // 将error传递下去交给catch处理
    const defaultOnRejected = (err) => {
      throw err;
    };
    const defaultOnFulfilled = (value) => {
      return value;
    };
    onRejected = onRejected || defaultOnRejected;

    onFulfilled = onFulfilled || defaultOnFulfilled;
    // 返回一个promise,就可以进行链式调用了
    return new myPromise((resolve, reject) => {
      //当调用then的时候，状态已经确定
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject);
      }

      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject);
      }
      // 当状态还没有发生改变的时候,将成功的回调和失败的回调放入数组
      if (this.status === PROMISE_STATUS_PENDING) {
        if (onFulfilled) {
          this.onFulfilledFns.push(() => {
            execFunctionWithCatchError(
              onFulfilled,
              this.value,
              resolve,
              reject
            );
          });
        }
        if (onRejected) {
          this.onRejectedFns.push(() => {
            execFunctionWithCatchError(
              onRejected,
              this.reason,
              resolve,
              reject
            );
          });
        }
      }
    });
  }
  catch(onRejected) {
    return this.then(undefined, onRejected);
  }

  finally(onfinally) {
    this.then(
      () => {
        onfinally();
      },
      () => {
        onfinally();
      }
    );
  }
  // 就是将value转成fulfilled状态可以进行then调用
  static resolve(value) {
    return new myPromise((resolve) => {
      return resolve(value);
    });
  }
  // 将reason装成rejected状态
  static reject(reason) {
    return new myPromise((resolve, reject) => {
      return reject(reason);
    });
  }
  // all传入一个promise数组，如果promise item状态都是fulfulled，all才是fuldilled状态，否则只要出现了reject就会变成renject
  static all(promises) {
    return new myPromise((resolve, reject) => {
      const values = [];
      promises.forEach((promise) => {
        promise.then(
          (res) => {
            values.push(res);
            if (values.length === promises.length) {
              resolve(values);
            }
          },
          (err) => {
            reject(err);
          }
        );
      });
    });
  }

  static allSettled(promises) {
    return new myPromise((resolve, reject) => {
      const values = [];
      promises.forEach((promise) => {
        promise.then(
          (res) => {
            values.push({
              status: PROMISE_STATUS_FULFILLED,
              value: res,
            });

            if (values.length === promises.length) {
              resolve(values);
            }
          },
          (err) => {
            values.push({ status: PROMISE_STATUS_REJECTED, value: err });
            if (values.length === promises.length) {
              resolve(values);
            }
          }
        );
      });
    });
  }
  // 相当于是竞赛，谁的状态先改变就用谁的
  static race(promsies) {
    return new myPromise((resolve, reject) => {
      promsies.forEach((promise) => {
        promise.then(resolve, reject);
      });
    });
  }
  //有一个状态时fulfilled状态就是执行resolve,等所有都变成rejected状态才会是reject  和all相反
  static any(promises) {
    return new myPromise((resolve, reject) => {
      const reasons = [];
      promises.forEach((promise) => {
        promise.then(
          (res) => {
            resolve(res);
          },
          (err) => {
            reasons.push(err);
            if (reasons.length === promises.length) {
              reject(new AggregateError(reasons));
            }
          }
        );
      });
    });
  }
}

const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(1111);
  }, 3000);
});
const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(2222);
  }, 2000);
});
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(3333);
  }, 3000);
});

myPromise
  .race([p1, p2, p3])
  .then((res) => {
    console.log("res:", res);
  })
  .catch((err) => {
    console.log("err:", err);
  });

myPromise
  .any([p1, p2, p3])
  .then((res) => {
    console.log("res:", res);
  })
  .catch((err) => {
    console.log("err:", err.errors);
  });

```

