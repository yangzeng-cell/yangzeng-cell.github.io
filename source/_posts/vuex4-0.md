---
title: vuex4.0
date: 2022-08-23 10:22:47
tags:
categories:
- [vue, note]
---

#### Vuex 是什么

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式 + 库**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

![](https://vuex.vuejs.org/vuex.png)

```
npm install vuex@next --save
```

```js
//store/index.js
import { createStore } from 'vuex'
import { CHANGE_INFO } from './mutation_types'

import homeModule from './modules/home'
import counterModule from './modules/counter'

const store = createStore({
  state: () => ({
    // 模拟数据
    // counter: 100,
    rootCounter: 100,
    name: "coderwhy",
    level: 100,
    avatarURL: "http://xxxxxx",
    friends: [
      { id: 111, name: "why", age: 20 },
      { id: 112, name: "kobe", age: 30 },
      { id: 113, name: "james", age: 25 }
    ],

    // 服务器数据
    // banners: [],
    // recommends: []
  }),
  getters: {
    // 1.基本使用
    doubleCounter(state) {
      return state.counter * 2
    },
    totalAge(state) {
      return state.friends.reduce((preValue, item) => {
        return preValue + item.age
      }, 0)
    },
    // 2.在该getters属性中, 获取其他的getters
    message(state, getters) {
      return `name:${state.name} level:${state.level} 						friendTotalAge:${getters.totalAge}`
    },
    // 3.getters是可以返回一个函数的, 调用这个函数可以传入参数(了解)
    getFriendById(state) {
      return function(id) {
        const friend = state.friends.find(item => item.id === id)
        return friend
      }
    }
  },
  mutations: {
    increment(state) {
      state.counter++
    },
    changeName(state, payload) {
      state.name = payload
    },
    incrementLevel(state) {
      state.level++
    },
     //使用计算属性，将常量作为参数名
    [CHANGE_INFO](state, newInfo) {
      state.level = newInfo.level
      state.name = newInfo.name

      // 重要的原则: 不要在mutation方法中执行异步操作
      // fetch("xxxx").then(res => {
      //   res.json().then(res => {
      //     state.name = res.name
      //   })
      // })
    },
    // changeBanners(state, banners) {
    //   state.banners = banners
    // },
    // changeRecommends(state, recommends) {
    //   state.recommends = recommends
    // }
  },
  actions: {
    incrementAction(context) {
      // console.log(context.commit) // 用于提交mutation
      // console.log(context.getters) // getters
      // console.log(context.state) // state
      context.commit("increment")
    },
    changeNameAction(context, payload) {
      context.commit("changeName", payload)
    },
    // fetchHomeMultidataAction(context) {
    //   // 1.返回Promise, 给Promise设置then
    //   // fetch("http://123.207.32.32:8000/home/multidata").then(res => {
    //   //   res.json().then(data => {
    //   //     console.log(data)
    //   //   })
    //   // })
      
    //   // 2.Promise链式调用
    //   // fetch("http://123.207.32.32:8000/home/multidata").then(res => {
    //   //   return res.json()
    //   // }).then(data => {
    //   //   console.log(data)
    //   // })
    //   return new Promise(async (resolve, reject) => {
    //     // 3.await/async
    //     const res = await fetch("http://123.207.32.32:8000/home/multidata")
    //     const data = await res.json()
        
    //     // 修改state数据
    //     context.commit("changeBanners", data.data.banner.list)
    //     context.commit("changeRecommends", data.data.recommend.list)

    //     resolve("aaaaa")
    //   })
    // }
  },
  modules: {
    home: homeModule,
    counter: counterModule
  }
})

export default store
```

```
//main.js
import { createApp } from 'vue'
import App from './App.vue'
import store from './store'

createApp(App).use(store).mount('#app')
```

#### State

state中主要是管理全局的状态，所有状态都存储在state中

```
 //state要放到computed中去使用
 export default {
    computed: {
      storeCounter() {
        return this.$store.state.counter
      }
    }
  }
```

```js
//在setup中使用state 需要使用useStore
<script setup>
  import { toRefs } from 'vue'
  import { useStore } from 'vuex'

  const store = useStore()
  // 如果对store.state进行解构或者赋值给另外一个变量，会变成非响应式的，要使用toRefs
  const { counter } = toRefs(store.state)
  
  function increment() {
    // store.state.counter++
    store.commit("increment")
  }
</script>
```

##### `mapState` 辅助函数

`mapState` 辅助函数帮助我们生成计算属性

```
/ 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

```
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])//还可以传递数组
```

```
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```

//使用展开运算符和其他computed合并

```js
 computed: {
      fullname() {
        return "xxx"
      },
      // name() {
      //   return this.$store.state.name
      // },
      ...mapState(["name", "level", "avatarURL"]),
      ...mapState({
        sName: state => state.name,
        sLevel: state => state.level
      })
    }
  }
```

##### Getter

getter类似于vue的computed

Getter 接受 state 作为其第一个参数

```js
const store = createStore({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos (state) {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

##### 通过属性访问

Getter 会暴露为 `store.getters` 对象，你可以以属性的形式访问这些值：

```
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

Getter 也可以接受其他 getter 作为第二个参数：

```
getters: {
  // ...
  doneTodosCount (state, getters) {
    return getters.doneTodos.length
  }
}
store.getters.doneTodosCount // -> 1
```

我们可以很容易地在任何组件中使用它：

```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```

注意，getter 在通过属性访问时是作为 Vue 的响应式系统的一部分缓存其中的。

##### 通过方法访问

你也可以通过让 getter 返回一个函数，来实现给 getter 传参。在你对 store 里的数组进行查询时非常有用。

```
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```

注意，getter 在通过方法访问时，每次都会去进行调用，而不会缓存结果

##### `mapGetters` 辅助函数

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

```
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})//还可以取别名
```

##### 在setup中使用

```js
<script setup>

  import { computed, toRefs } from 'vue';
  import { mapGetters, useStore } from 'vuex'

  const store = useStore()

  // 1.使用mapGetters
  // const { message: messageFn } = mapGetters(["message"])
  // const message = computed(messageFn.bind({ $store: store }))

  // 2.直接解构, 并且包裹成ref
  // const { message } = toRefs(store.getters)

  // 3.针对某一个getters属性使用computed
  const message = computed(() => store.getters.message)

  function changeAge() {
    store.state.name = "kobe"
  }

</script>

```

#### Mutation

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation

mutation的使用

```js
 import { CHANGE_INFO } from "@/store/mutation_types"

  export default {
    computed: {
    },
    methods: {
      changeName() {
        // this.$store.state.name = "李银河"
        //传参方式
        this.$store.commit("changeName", "王小波")
      },
      incrementLevel() {
        this.$store.commit("incrementLevel")
      },
      changeInfo() {
         //这是定义常量的方式
        this.$store.commit(CHANGE_INFO, {
          name: "王二",
          level: 200
        })
      }
    }
  }
```

```
//在mutation中commit
const store = createStore({
  state: () => ({
    name: "张三",
  }),
  mutations: {
    updateMut(state, playload) {
      state.name = playload;
    },
    updateMut2(state, playload) {
      store.commit("updateMut", playload);
    },
  },
});
```

##### Mutation 必须是同步函数

```
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```

我们正在 debug 一个 app 并且观察 devtool 中的 mutation 日志。每一条 mutation 被记录，devtools 都需要捕捉到前一状态和后一状态的快照。然而，在上面的例子中 mutation 中的异步函数中的回调让这不可能完成：因为当 mutation 触发的时候，回调函数还没有被调用，devtools 不知道什么时候回调函数实际上被调用——实质上任何在回调函数中进行的状态的改变都是不可追踪的。

##### mapMutations

```
methods: {
      btnClick() {
        console.log("btnClick")
      },
      // ...mapMutations(["changeName", "incrementLevel", CHANGE_INFO])  使用常量，在template中要使用正确的函数名
    }
```

#### Action

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

Action 通过 `store.dispatch` 方法触发：

```
store.dispatch('increment')
```

```
//在action中也可以分发action
actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```

```
 //在组件中使用
 methods: {
      // counterBtnClick() {
      //   this.$store.dispatch("incrementAction")
      // },
      // nameBtnClick() {
      //   this.$store.dispatch("changeNameAction", "aaa")
      // }
      // ...mapActions(["incrementAction", "changeNameAction"])
    }
```

#### Modules的使用

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

当没有添加namaspaced默认情况下使用module的值

```
<template>
  <div class="home">
    <h2>Home Page</h2>
    <!-- 1.使用state时, 是需要state.moduleName.xxx -->
    <h2>Counter模块的counter: {{ $store.state.counter.count }}</h2>
    <!-- 2.使用getters时, 是直接getters.xxx -->
    <h2>Counter模块的doubleCounter: {{ $store.getters.doubleCount }}</h2>

    <button @click="incrementCount">count模块+1</button>
  </div>
</template>

<script>
</script>

<script setup>

  import { useStore } from 'vuex'

  // 告诉Vuex发起网络请求
  const store = useStore()
  // 派发事件时, 默认也是不需要跟模块名称
  // 提交mutation时, 默认也是不需要跟模块名称
  function incrementCount() {
    store.dispatch("incrementCountAction")
  }

</script>
```

对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：

```js
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

对于模块内部的 getter，根节点状态会作为第三个参数暴露出来：

```js
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```

##### 命名空间

默认情况下，模块内部的 action 和 mutation 仍然是注册在**全局命名空间**的——这样使得多个模块能够对同一个 action 或 mutation 作出响应。Getter 同样也默认注册在全局命名空间，但是目前这并非出于功能上的目的（仅仅是维持现状来避免非兼容性变更）。必须注意，不要在不同的、无命名空间的模块中定义两个相同的 getter 从而导致错误。

如果希望你的模块具有更高的封装度和复用性，你可以通过添加 `namespaced: true` 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。例如：

```js
const store = createStore({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: () => ({ ... }), // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: () => ({ ... }),
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: () => ({ ... }),
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```

```
//在组件中的写法
<template>
  <div class="home">
    <h2>Home Page</h2>
    <!-- 1.使用state时, 是需要state.moduleName.xxx -->
    <h2>Counter模块的counter: {{ $store.state.counter.count }}</h2>
    <!-- 2.使用getters时, 是直接getters.xxx -->
    <h2>Counter模块的doubleCounter: {{ $store.getters["counter/doubleCount"] }}</h2>

    <button @click="incrementCount">count模块+1</button>
  </div>
</template>

<script>
</script>

<script setup>

  import { useStore } from 'vuex'

  // 告诉Vuex发起网络请求
  const store = useStore()
  // 派发事件时, 默认也是不需要跟模块名称
  // 提交mutation时, 默认也是不需要跟模块名称
  function incrementCount() {
    store.dispatch("counter/incrementCountAction")//有命名空间的写法
  }

</script>
```

启用了命名空间的 getter 和 action 会收到局部化的 `getter`，`dispatch` 和 `commit`。换言之，你在使用模块内容（module assets）时不需要在同一模块内额外添加空间名前缀。更改 `namespaced` 属性后不需要修改模块内的代码。

##### 在带命名空间的模块内访问全局内容

如果你希望使用全局 state 和 getter，`rootState` 和 `rootGetters` 会作为第三和第四参数传入 getter，也会通过 `context` 对象的属性传入 action。

```js
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
        rootGetters['bar/someOtherGetter'] // -> 'bar/someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'
        rootGetters['bar/someGetter'] // -> 'bar/someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```
