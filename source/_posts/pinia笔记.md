---
title: pinia笔记
date: 2022-08-23 15:53:55
tags:
categories:
- [vue, note]
---

#### Pinia和Vuex的区别

Pinia主要是来替代Vuex的，pinia有更加简单的api，提供了composition api,和typesctript一起使用有更加可靠的类型推断支持

和vuex相比，pinia有很多优势

例如mutation不再存在

更好的typescript支持，vuex之前对ts的支持很不友好

不再有modules的嵌套解构，你可以灵活的使用每一个store，他们是通过扁平化的方式来进行相互使用

不再有命名空间的概念，不需要记住他们的复杂关系

#### 安装

```
yarn add pinia
# or with npm
npm install pinia
```

#### 使用

在vue3+vite中的使用

stores/index.js

```
import { createPinia } from 'pinia'

const pinia = createPinia()

export default pinia

```

main.js

```
import { createApp } from 'vue'
import App from './App.vue'
import pinia from './stores'

createApp(App).use(pinia).mount('#app')
```

#### 认识Store

##### 什么是store

一个store是一个实体，他会持有为绑定到组件树的状态和业务逻辑，换句话说，**它托管全局状态**。它有点像一个始终存在并且每个人都可以读取和写入的组件。你可以定义任意个store来管理你的状态。

##### store有三个核心概念

state，actions，getters，对应者数组的data，computed，methods

一旦store被实例化，你就可以直接在store上访问state，getters，actions中的任意属性

#### 定义Store

```js
import { defineStore } from 'pinia'

const useUser = defineStore("user", {
  state: () => ({
    name: "why",
    age: 18,
    level: 100
  })
})

export default useUser
//使用defineStore来定义store
```

与Vue的Option API 类型类似的写法

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0, name: 'Eduardo' }),
  getters: {
    doubleCount: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

还有一种和setup函数类似的方法。我们可以传入一个定义响应式属性和方法的函数，并返回一个包含我们想要公开的属性和方法的对象。

```js
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)//state
  const name = ref('Eduardo')
  const doubleCount = computed(() => count.value * 2)//getters
  function increment() {
    count.value++
  }//actions

  return { count, name, doubleCount, increment }
})
```

在组件中的使用

```js
<script setup>
  import { toRefs } from 'vue'
  import { storeToRefs } from 'pinia'
  import useCounter from '@/stores/counter';

  const counterStore = useCounter()
//如果需要解构state并保持响应性，可以使用toRefs/storeToRefs
// const { count } = toRefs(counterStore)
  const { count } = storeToRefs(counterStore)
  function incrementCount() {
    counterStore.count++
  }

</script>
```

#### `state`的使用

```
const store = useStore()

store.count++
```

重置state

```
const store = useStore()

store.$reset()
```

```
 // 1.一个个修改状态
     userStore.name = "kobe"
     userStore.age = 20
     userStore.level = 200

    // 2.一次性修改多个状态
     userStore.$patch({
       name: "james",
       age: 35
     })
  //$patch还可以传入一个函数 
 cartStore.$patch((state) => {
  state.items.push({ name: 'shoes', quantity: 1 })
  state.hasChanged = true
})
```

替换state

你不能替换整个state,可以整合它

```
store.$patch({ count: 24 })
```

#### Getters

```js
getters: {
    // 1.基本使用
    doubleCount(state) {
      return state.count * 2
    },
    // 2.一个getter引入另外一个getter
    doubleCountAddOne() {
      // this是store实例
      return this.doubleCount + 1
    },
    // 3.getters也支持返回一个函数
    getFriendById(state) {
      return function(id) {
        for (let i = 0; i < state.friends.length; i++) {
          const friend = state.friends[i]
          if (friend.id === id) {
            return friend
          }
        }
      }
    },
    // 4.getters中用到别的store中的数据
    showMessage(state) {
      // 1.获取user信息
      const userStore = useUser()

      // 2.获取自己的信息

      // 3.拼接信息
      return `name:${userStore.name}-count:${state.count}`
    }
  },
```

#### Actions

actions相当于是methods

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0,
  }),
  actions: {
    // since we rely on `this`, we cannot use an arrow function
      //这里依赖this,所以不能使用箭头函数
    increment() {
      this.count++
    },
    randomizeCounter() {
      this.count = Math.round(100 * Math.random())
    },
  },
})
```

在actions中可以定义异步函数

```js
import { mande } from 'mande'

const api = mande('/api/users')

export const useUsers = defineStore('users', {
  state: () => ({
    userData: null,
    // ...
  }),

  actions: {
    async registerUser(login, password) {
      try {
        this.userData = await api.post({ login, password })
        showTooltip(`Welcome back ${this.userData.name}!`)
      } catch (error) {
        showTooltip(error)
        // let the form component display the error
        return error
      }
    },
  },
})
```
