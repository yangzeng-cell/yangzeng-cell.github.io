---
title: 前端八股文-Vue3全家桶实战
date: 2022-09-04 10:54:23
tags:
- [Vue3]
categories:
- [前端八股文]
---

# Vue常⻅⾯试题

## Vue基础

Vue (读⾳ /vjuː/，类似于 view) 是⼀套⽤于构建⽤户界⾯的渐进式 JavaScript框架

- 全称是Vue.js或者Vuejs；
- 它基于标准 HTML、CSS 和 JavaScript 构建，并提供了⼀套声明式的、组件化的编程模型；
- 帮助你⾼效地开发⽤户界⾯，⽆论任务是简单还是复杂；

### Vue.js 的特点

- 易⽤： Vuejs是⼀个渐进式的框架，相⽐于其它框架，它更简单，易学，上⼿快。
- 灵活： （渐进式）不断繁荣的⽣态系统，可以在⼀个库和⼀套完整框架之间⾃如伸缩。
- ⾼效： 20kB min+gzip 运⾏⼤⼩；超快虚拟 DOM；最省⼼的优化。
- 双向绑定：开发效率⾼。
- 基于组件的代码共享
- Web项⽬⼯程化，增加可读性、可维护性

### 什么是 MVVM

MVC和MVVM都是⼀种软件的体系结构

- MVC是Model – View –Controller的简称，是在前期被使⽤⾮常框架的架构模式，⽐如iOS、前端；
- MVVM是Model-View-ViewModel的简称，是⽬前⾮常流⾏的架构模式；

通常情况下，我们也经常称Vue是⼀个MVVM的框架。

- Vue官⽅其实有说明，Vue虽然并没有完全遵守MVVM的模型，但是整个设计是受到它的启发的。

### 说说你对 SPA 单⻚⾯的理解，它的优缺点分别是什么

SPA（ single-page application ）仅在 Web ⻚⾯初始化时加载相应的 HTML、JavaScript 和 CSS。⼀旦⻚⾯加载完成，SPA 不会因为⽤户的操作⽽进⾏⻚⾯的重新加载或跳转；取⽽代之的是利⽤路由机制实现 HTML 内容的变换，UI 与⽤户的交互，避免⻚⾯的重新加载。

**优点：**

- ⽤户体验好、快，内容的改变不需要重新加载整个⻚⾯，避免了不必要的跳转和重复渲染；
- 基于上⾯⼀点，SPA 相对对服务器压⼒⼩；
- 前后端职责分离，架构清晰，前端进⾏交互逻辑，后端负责数据处理；

**缺点：**

- 初次加载耗时多：为实现单⻚ Web 应⽤功能及显示效果，需要在加载⻚⾯的时候将 JavaScript、CSS 统⼀加载，部分⻚⾯按需加载；
- 前进后退路由管理：由于单⻚应⽤在⼀个⻚⾯中显示所有的内容，所以不能使⽤浏览器的前进后退功能，所有的⻚⾯切换需要⾃⼰建⽴堆栈管理；
- SEO 难度较⼤：由于所有的内容都在⼀个⻚⾯中动态替换显示，所以在 SEO 上其有着天然的弱势

### v-show 与 v-if 有什么区别

**⾸先，在⽤法上的区别：**

- v-show是不⽀持template；
- v-show不可以和v-else⼀起使⽤；

**其次，本质的区别：**

- v-show元素⽆论是否需要显示到浏览器上，它的DOM实际都是有存在的，只是通过CSS的display属性来进⾏切换；
- v-if当条件为false时，其对应的原⽣压根不会被渲染到DOM中；

**开发中如何进⾏选择呢？**

- 如果我们的原⽣需要在显示和隐藏之间频繁的切换，那么使⽤v-show；
- 如果不会频繁的发⽣切换，那么使⽤v-if；

### 数组中的哪些⽅法会触发视图的更新

Vue 将被侦听的数组的变更⽅法进⾏了包裹，所以它们也将会触发视图更新，这些被包裹过的⽅法包括：

push()

pop()

shift()

unshift()

splice()

sort()

reverse()

上⾯的⽅法会直接修改原来的数组，所以它们会触发视图更新

其它数组的⽅法:

但是某些⽅法不会替换原来的数组，⽽是会⽣成新的数组，⽐如 fifilter()、concat() 和 slice()，使⽤这些⽅法将不会触发视图更新

### Vue中v-for的key 有什么作⽤

在使⽤v-for进⾏列表渲染时，我们通常会给元素或者组件绑定⼀个key属性。

这个key属性有什么作⽤呢？

- key属性主要⽤在Vue的虚拟DOM算法，在新旧nodes对⽐时辨识VNodes。
- 如果不使⽤key，Vue会使⽤⼀种最⼤限度减少动态元素并且尽可能的尝试就地修改/复⽤相同类型元素的算法
- 使⽤key时，它会基于key的变化重新排列元素顺序，并且会移除/销毁key不存在的元素。

key 是 VNode 的唯⼀标记，通过这个 key， diffff 操作可以更准确、更快速的达到复⽤节点，更新视图的⽬的。复⽤节点就需要通过移动元素的位置来达到更新的⽬的。

### computed和method有什么区别

计算属性和⽅法：

- 都可以通过this来访问
- 都可以对⼀些数据进⾏处理和计算
- 对于包含响应式数据计算的逻辑，应该使⽤计算属性，因为计算属性是有缓存。

computed和method的区别

- computed底层会缓存, 性能更⾼
- 计算属性会基于它们的依赖关系进⾏缓存;
- 在数据不发⽣变化时，计算属性是不需要重新计算的
- 但是如果依赖的数据发⽣变化，在使⽤时，计算属性依然会重新进⾏计算

###  什么是双向绑定？v-model的本质是什么

双向绑定:

- 即当数据发⽣变化的时候，视图也就发⽣变化，当视图发⽣变化的时候，数据也会跟着同步变化
- v-model 是语法糖，它负责监听⽤户在表单元素中的输⼊事件来更新数据

表单元素使⽤v-model的本质：

- v-bind绑定value属性的值
- v-on绑定input事件监听到函数,函数会获取最新的值赋值到绑定的属性中

```jsx
<input type="text" :value="message" @input="message = $event.target.value" />
```

组件使⽤v-model的本质:

- 将其 value attribute 绑定到⼀个名叫 modelValue 的 prop 上；
- 在其 input 事件被触发时，将新的值通过⾃定义的 update:modelValue 事件抛出(发出)；

```jsx
<Counter v-model="appCounter"/>
<!-- 相当于-->
<Counter v-bind:modelValue="appCounter" @update:modelValue="appCounter =
$event"/>
```

### data选项为什么是⼀个函数⽽不是对象

JavaScript中的对象是引⽤类型的数据，当多个实例引⽤同⼀个对象时，只要⼀个实例对这个对象进⾏操作，其他实例中的数据也会发⽣变化。

⽽在Vue中，我们更多的是想要复⽤组件，那就需要每个组件都有⾃⼰的数据，这样组件之间才不会相互⼲扰。

所以组件的数据不能写成对象的形式，⽽是要写成函数的形式。数据以函数返回值的形式定义。

这样当我们每次复⽤组件的时候，就会返回⼀个新的data，也就是说每个组件都有⾃⼰的私有数据空间，它们各⾃维护⾃⼰的数据，不会⼲扰其他组件的正常运⾏

###  Vue data 中某⼀个属性的值发⽣改变后，视图会⽴即同步执⾏重新渲染吗

不会⽴即同步执⾏重新渲染。

Vue 实现响应式并不是数据发⽣变化之后 DOM ⽴即变化，⽽是按⼀定的策略进⾏ DOM 的更新。

Vue 在更新 DOM 时是异步执⾏的。只要侦听到数据变化， Vue 将开启⼀个队列，并缓冲在同⼀事件循环中发⽣的所有数据变更。

如果同⼀个watcher被多次触发，只会被推⼊到队列中⼀次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是⾮常重要的。

然后，在下⼀个的事件循环”tick”中，Vue 刷新队列并执⾏实际（已去重的）⼯作

### sass是什么？如何在vue中安装和使⽤

sass是⼀种CSS预处理器语⾔，除此之外，less、stylus也是常⻅的CSS预处理器语⾔。

sass安装和使⽤步骤如下：

1. ⽤npm安装加载程序（ sass-loader、 css-loader等加载程序)。

2. 在 webpack.confifig.js中配置sass加载程序

### 在 Vue. js开发环境下调⽤API接⼝，如何避免跨域

1.在vue.confifig.js中的devServer选项中的proxy中配置反向代理

2.在vite.confifig.js中的server选项中的proxy中配置反向代理

3.直接后端开发⼈员配置cors

###  v-if和v-for⼀起使⽤的弊端及解决办法

Vue.js 中使⽤最多的两个指令就是 v-if 和 v-for ，因此开发者们可能会想要同时使⽤它们。虽然不建议这样做，但有时确实是必须的，于是我们想提供有关其⼯作⽅式的指南。

- 2..x 版本中在⼀个元素上同时使⽤ v-if 和 v-for 时， v-for 会优先作⽤。
- 3.x 版本中 v-if 总是优先于 v-for ⽣效。

由于语法上存在歧义，建议避免在同⼀元素上同时使⽤两者。

⽐起在模板层⾯管理相关逻辑，更好的办法是通过创建计算属性筛选出列表，并以此创建可⻅元素，⽐如：

1. 在v-for的外层或内层包裹⼀个元素（template）来使⽤v-if

2. ⽤computed处理筛选出列表

### 谈谈你对 keep-alive 的了解

keep-alive 是 Vue 内置的⼀个组件，可以使被包含的组件保留状态，避免重新渲染 ，其有以下特性：

- ⼀般结合路由和动态组件⼀起使⽤，⽤于缓存组件。
- 提供 include 和 exclude 属性，两者都⽀持字符串或正则表达式。

​			include 表示只有名称匹配的组件会被缓存。

​			exclude 表示任何名称匹配的组件都不会被缓存。

​			其中 exclude 的优先级⽐ include ⾼。

- 对应两个钩⼦函数 activated 和 deactivated 。

​			当组件被激活时，触发钩⼦函数 activated。

​			当组件被移除时，触发钩⼦函数 deactivated。

### 说说Vue插槽的作⽤和平时开发中的应⽤

插槽的作⽤:

- ⽀持在⽗组件⾃定义⼦组件中的个内容
- 让⼦组件更具有通⽤性，不必限定死某个内容

插槽平时开发中的应⽤:

- 在封装组件时，如果组件中的某个内容是动态的或不确定的，就可以使⽤插槽来代替了。
- 在使⽤第三⽅库时，往往会通过使⽤插槽类⾃定义第三⽅组件中的某些内容。

## Component组件

### ⽗⼦组件的⽣命周期顺序

加载渲染过程： ⽗beforeCreate -> ⽗created -> ⽗beforeMount -> ⼦beforeCreate -> ⼦created -> ⼦beforeMount ->⼦mounted -> ⽗mounted

⼦组件更新过程：⽗beforeUpdate -> ⼦beforeUpdate -> ⼦updated -> ⽗updated

⽗组件更新过程：⽗beforeUpdate -> ⽗updated

销毁过程：⽗beforeDestroy -> ⼦beforeDestroy -> ⼦destroyed -> ⽗destroyed

### 组件通讯(传值)式有哪些

- ⽗传⼦：⼦组件通过props来接收⽗组件传递的属性 xxx 的值
- ⼦传⽗：⼦组件通过emit触发事件传递，⽗组件通过监听对应的事件来接收数据
- Provide/Inject：⽗组件提供内容，⼦或孙组件可以注⼊⽗组件提供的内容。
- 组件实例：通过ref来拿到组件的实例，调⽤实例的属性或⽅法进⾏传值。
- 事件总线：可以⾃⼰编写EventBus插件来进⾏通讯，或世界使⽤第三⽅的事件总线库。
- ⽤Vuex/Pinia: 可以使⽤全局状态管理来进⾏全局共享数据

###  什么是⽣命周期函数？Vue组件的⽣命周期函数有哪些

**⽣命周期函数：**

⽣命周期函数是⼀些钩⼦函数（回调函数），在某个时间会被Vue源码内部进⾏回调

通过对⽣命周期函数的回调，我们可以知道⽬前组件正在经历什么阶段

Vue3的⽣命周期函数:

beforeCreate :组件实例在创建之前

created: 组件被创建完成

​	可以发送⽹络请求

​	可以事件监听

​	this.$watch()

beforeMount : 组件template准备被挂载

mounted :组件template已经被挂载

​	可以获取DOM,可以使⽤DOM

beforeUpdate: 准备更新DOM

updated: 更新DOM,根据最新数据⽣成新的VNode,⽣成新的虚拟DOM,转换为真实的DOM

beforeUnmount: 卸载之前

unmounted: DOM 元素被卸载完成

​	回收操作(取消事件监听)

## Composition API

### 什么是Composition API 和 Options API

Composition API:

- Composition API 是⼀组 API，允许我们使⽤导⼊的函数⽽不是声明选项来编写 Vue 组件。它是⼀个涵盖以下 API 的总称：Reactivity API、Lifecycle Hooks、Dependency Injection等等
- 使⽤Composition API编写组件时可以根据逻辑功能来组织代码。⽐如可以把⼀个功能所⽤到的API 放在⼀起，这样可以让代码⾼内聚和低耦合，进⽽提⾼了代码的逻辑的复⽤性。
- 在 Vue 3 中，它也主要与script setup语法⼀起使⽤

Options API:

- 在对应的属性中编写对应的功能模块, ⽐如data定义数据、methods中定义⽅法、computed中定义计算属性、watch中监听属性改变，也包括⽣命周期钩⼦
- 弊端: 当我们实现某⼀个功能时，这个功能对应的代码逻辑会被拆分到各个属性中,当组件变得复杂，导致对应属性的列表也会增⻓，这可能会导致组件难以阅读和理解

### Composition API和之Options API有什么区别

- 在逻辑组织和逻辑复⽤⽅⾯，Composition API是优于Options API。
- Composition API⼏乎是函数，会有更好的类型推断，对于TS的⽀持更友好。
- Composition API对 tree-shaking 友好，代码也更容易压缩。
- Composition API中⻅不到this的使⽤，减少了this指向不明的情况。
- Composition API⽤起来稍微复杂⼀点，⽽Options API就⾮常简单、易于使⽤

### 说说Vue3中setup函数的作⽤

在Vue3中， setup() 函数充当了组件编写Composition API 的⼊⼝点

setup函数参数主要有两个参数：

​	第⼀个参数：props , ⽗组件传递过来的属性会被放到props对象中

​	第⼆个参数：context, 它⾥⾯包含三个属性

​		attrs：所有的⾮prop的attribute；

​		slots：⽗组件传递过来的插槽；

​		emit：当我们组件内部需要发出事件时会⽤到emit（因为我们不能访问this，所以不可

以通过 this.$emit发出事件）

可以在setup中可以定义响应式数据、⽅法、计算属性、侦听器等等。

可以通过setup的返回值来替代data选项，让数据可以直接在template中使⽤。

### ref和reactive有什么区别？开发中如何选择

- ref和reactive都是响应式的API，都可以⽤来定义响应式的数据。
- ref可以包裹任意数据类型，reactive只能包裹复杂数据类型，⽐如对象、数组。
- ref返回⼀个ref对象，在script中取值需要通过value属性，但是在模板中使⽤会进⾏解包不需要调⽤value。
- reactive包裹的是复杂数据类型，直接取⾥⾯的属性即可。
- ref⼏乎可以应⽤在任何场景，⽽且包含reactive适合的场景
- reactive的应⽤场景⽐较受限，第⼀：值⽐较固定，第⼆：值与值之间是有联系的。
- 开发中尽量选择ref

### Composition API常⻅的⼏个函数与⽤法

ref

​	包裹任意类型的值，将包裹的值加⼊响应式

reactive

​	包裹复杂类型的值，将包裹的值加⼊响应式

computed

​	把⼀些复杂逻辑⽤computed进⾏包裹，如同Options API中的计算属性⼀样

​	computed会⾃动收集相关依赖，当依赖发⽣变化时，会⾃动进⾏更新

⽣命周期

​	Vue3中想要在beforeCreate和created中做的事，直接在setup中做即可

​	Vue3的其他的⽣命周期函数都要在前⾯加⼀个on，然后需要在vue中主动引⼊watch

​	watch可以监听单个数据源，也可以监听多个数据源

​	watch是懒执⾏，第⼀次是不会执⾏的，除⾮你为其提供第三个参数中的immediate属性为

true

​	watch只有等到监听的数据源发⽣了变化后，才会执⾏第⼆个参数（回调）

​	watch可以获取监听数据源的前后变化的值

​	侦听多个数据源的时候，第⼀个参数是数组类型

watchEffect

​	watchEffffect会⾃动收集依赖，收集的依赖是第⼀个参数，也就是回调函数中有哪些东⻄是加⼊响应式的

​	如果这个值加⼊了响应式就会被收集起来，当被收集的值发⽣了变化，就会重新执⾏这个回调函数

​	watchEffffect第⼀次执⾏是在DOM挂载前执⾏的，所以如果你想在第⼀次执⾏时拿到DOM元 素

​	需要传⼊第⼆个参数，第⼆个参数是⼀个对象，让其flflush属性的值为post即可

toRefs

​	对reactive进⾏解构后就失去了响应式的效果，因为reactive返回的是⼀个Proxy对象

​	对Proxy对象进⾏解构，拿到的是纯净的值，所以没有了响应式的效果

​	如果想要对reactive进⾏解构，需要对其包裹⼀个toRefs

​	这么做相当于为reactive中的每⼀个值包裹了⼀个ref

### Vue3中的watch和watchEffffect有什么区别

watch和watchEffect都⽤⽤来侦听响应式数据的变化，watch可以侦听指定的源，默认第⼀次不会执⾏，watchEffffect虽不能指定侦听的源，但是会⾃动收集依赖，并默认会先执⾏⼀次。

watch

- ​	watch可以监听单个数据源，也可以监听多个数据源
- ​	watch是懒执⾏，第⼀次是不会执⾏的，除⾮你为其提供第三个参数中的immediate属性为true
- ​	watch只有等到监听的数据源发⽣了变化后，才会执⾏第⼆个参数（回调）
- ​	watch可以获取监听数据源的前后变化的值
- ​	侦听多个数据源的时候，第⼀个参数是数组类型

watchEffect

- ​	watchEffect会⾃动收集依赖，收集的依赖是第⼀个参数，也就是回调函数中有哪些东⻄是加⼊响应式的
- ​	如果这个值加⼊了响应式就会被收集起来，当被收集的值发⽣了变化，就会重新执⾏这个回调函数
- ​	watchEffect第⼀次执⾏是在DOM挂载前执⾏的，所以如果你想在第⼀次执⾏时拿到DOM元素
- ​	需要传⼊第⼆个参数，第⼆个参数是⼀个对象，让其flflush属性的值为post即可

### 说说Vue3中script setup语法糖常⻅⽤法

script setup 是在单⽂件组件中使⽤ Composition API 的编译时语法糖，相⽐与之前的setup函数写法，它具有更多的优势：

- 更少的样板内容，更简洁的代码。
- 能够使⽤纯 TypeScript 声明 props 和抛出事件。
- 更好的运⾏时性能 (其模板会被编译成与其同⼀作⽤域的渲染函数，没有任何的中间代理)。
- 更好的 IDE 类型推断性能 (减少语⾔服务器从代码中抽离类型的⼯作)。

**script setup**

- 当使⽤ script setup 的时候，任何在 script setup 声明的顶层绑定都能在模板中直接使⽤

  ​	声明的顶层绑定：包括变量，函数声明，以及 import 引⼊的内容

- 响应式数据需要通过ref、reactive来创建

- 在script setup中导⼊的组件可以直接使⽤

**defifineProps**

在script setup语法糖中必须使⽤ defifineProps API来声明props，它具备完整的类型推断并且在<script setup> 中是直接可⽤的（不需要额外导⼊）。

**defifineEmits**

在script setup语法糖中必须使⽤ defifineEmits API来声明 emits，它具备完整的类型推断并且在<script setup> 中是直接可⽤的（不需要额外导⼊）。

**defifineExpose**

- 获取组件的实例可以通过ref来获取，接着组件挂载完成后可通过value拿到组件实例。
- 当拿到组件实例后，默认是不可以访问这个实例中的⽅法和属性，因为默认没暴露任何⽅法和属性。
- 因此在Vue3组件中可以⽤defifineExpose API来暴露⽅法和属性给外部访问。
- defifineExpose 也是不需要导⼊，直接使⽤即可
