---
title: 实现可过期的localstorage数据(仅供参考)
date: 2022-08-15 10:41:58
tags:
categories:
- [javascript, 搜集]
---

localstorage相对于cookie中的问题是不能设置过期时间

所以我们要自己对localStorage进行二次封装，使得localstorage在适当的时间失效

#### 解法一

```
localStorage.setItem("key", "value");
const timeout = 1000 * 60 * 60;
setTimeout(() => {
  localStorage.removeItem("key");
}, timeout);
```

这种方式需要编写多个定时器，维护成本高，不利于代码的复用

#### 解法二

1. 用**「localStorage」**存一份{key(键): expire(过期时间)}的映射表
2. 重写**「localStorage API」**, 对方法进行二次封装

```
const store = {
  setExpireMap(key, expire) {
    const expireMap = localStorage.getItem("EXPIRE_MAP") || {};
    localStorage.setItem(
      "EXPIRE_MAP",
      JSON.stringify({
        ...expireMap,
        [key]: expire,
      })
    );
  },
  setItem(key, value, expire) {
    this.setExpireMap(key, expire);
    localStorage.setItem(key, value);
  },
  getItem(key) {
    const expireMap = JSON.parse(localStorage.getItem("EXPIRE_MAP"))[key];
    if (expireMap && expireMap > Date.now()) {
      return localStorage.getItem(key);
    }
    localStorage.removeItem(key);
    return null;
  },
};
```

- 对 `store` 操作时需要维护2份数据, 并且占用缓存空间
- 如果 `EXPIRE_MAP` 误删除将会导致所有过期时间失效
- 对操作过程缺少更灵活的控制(比如操作状态, 操作回调等)

#### 解法三

惰性删除，只有在下一次取值的时候才删除

```
var lsc = (function (self) {
    var prefix = 'one_more_lsc_'
    /**
     * 增加一个键值对数据
     * @param key 键
     * @param val 值
     * @param expires 过期时间，单位为秒
     */
    self.set = function (key, val, expires) {
        key = prefix + key;
        val = JSON.stringify({'val': val, 'expires': new Date().getTime() + expires * 1000});
        localStorage.setItem(key, val);
    };
    /**
     * 读取对应键的值数据
     * @param key 键
     * @returns {null|*} 对应键的值
     */
    self.get = function (key) {
        key = prefix + key;
        var val = localStorage.getItem(key);
        if (!val) {
            return null;
        }
        val = JSON.parse(val);
        if (val.expires < new Date().getTime()) {
            localStorage.removeItem(key);
            return null;
        }
        return val.val;
    };
    return self;
}(lsc || {}));
```

上述代码通过惰性删除已经实现了可过期的localStorage缓存，但是也有比较明显的缺点：如果一个key一直没有被用到，即使它已经过期了也永远存放在localStorage。为了弥补这样缺点，我们引入另一种清理过期缓存的策略。

#### 解法四

### 定时删除

定时删除是指，每隔一段时间执行一次删除操作，并通过限制删除操作执行的次数和频率，来减少删除操作对CPU的长期占用。另一方面定时删除也有效的减少了因惰性删除带来的对localStorage空间的浪费。

每隔一秒执行一次定时删除，操作如下：

1. 随机测试20个设置了过期时间的key。
2. 删除所有发现的已过期的key。
3. 若删除的key超过5个则重复**步骤1**，直至重复500次。

具体实现如下：

```
var lsc = (function (self) {
    var prefix = 'one_more_lsc_'
    var list = [];
    //初始化list
    self.init = function () {
        var keys = Object.keys(localStorage);
        var reg = new RegExp('^' + prefix);
        var temp = [];
        //遍历所有localStorage中的所有key
        for (var i = 0; i < keys.length; i++) {
        	//找出可过期缓存的key
            if (reg.test(keys[i])) {
                temp.push(keys[i]);
            }
        }
        list = temp;
    };
    self.init();
    self.check = function () {
        if (!list || list.length == 0) {
            return;
        }
        var checkCount = 0;
        while (checkCount < 500) {
            var expireCount = 0;
            //随机测试20个设置了过期时间的key
            for (var i = 0; i < 20; i++) {
                if (list.length == 0) {
                    break;
                }
                var index = Math.floor(Math.random() * list.length);
                var key = list[index];
                var val = localStorage.getItem(list[index]);
                //从list中删除被惰性删除的key
                if (!val) {
                    list.splice(index, 1);
                    expireCount++;
                    continue;
                }
                val = JSON.parse(val);
                //删除所有发现的已过期的key
                if (val.expires < new Date().getTime()) {
                    list.splice(index, 1);
                    localStorage.removeItem(key);
                    expireCount++;
                }
            }
            //若删除的key不超过5个则跳出循环
            if (expireCount <= 5 || list.length == 0) {
                break;
            }
            checkCount++;
        }
    }
    //每隔一秒执行一次定时删除
    window.setInterval(self.check, 1000);
    return self;
}(lsc || {}));

```

