---
title: vue2和vue3的区别(vue2向vue3迁移)(二)
date: 2022-08-12 11:35:32
categories:
- [Vue, daily]
---
### 15.key属性

key主要适用于在Vue的虚拟DOM算法中，跟踪节点的，以便重用和修改现有的节点，以及何时修改或者重新排序。

在Vue2.x中，key也被推荐用于v-if,v-else,v-else-if

```json
<!-- Vue 2.x -->
<div v-if="condition" key="yes">Yes</div>
<div v-else key="no">No</div>
```

在vue3.x中不再要求这么使用，因为vue会自己给他添加一个unique key

```html
<!-- Vue 3.x -->
<div v-if="condition">Yes</div>
<div v-else>No</div>
```

如果你确实想添加key,必须保证key唯一

```html
<!-- Vue 2.x -->
<div v-if="condition" key="a">Yes</div>
<div v-else key="a">No</div>

<!-- Vue 3.x (recommended solution: remove keys) -->
<div v-if="condition">Yes</div>
<div v-else>No</div>

<!-- Vue 3.x (alternate solution: make sure the keys are always unique) -->
<div v-if="condition" key="a">Yes</div>
<div v-else key="b">No</div>

```

 **`<template v-for>`**

```
vue2.x中 <template>标签不能拥有key,只能在他的子元素中设置key
```

```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div :key="'heading-' + item.id">...</div>
  <span :key="'content-' + item.id">...</span>
</template>
```

在vue3.x中，key应该被设置在template上

<!-- Vue 3.x -->

<template v-for="item in list" :key="item.id">
  <div>...</div>
  <span>...</span>
</template>


v-for也是一样

```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div v-if="item.isVisible" :key="item.id">...</div>
  <span v-else :key="item.id">...</span>
</template>

<!-- Vue 3.x -->
<template v-for="item in list" :key="item.id">
  <div v-if="item.isVisible">...</div>
  <span v-else>...</span>
</template>
```

### 16.v-if 与 v-for 优先级

在vue2.x中，在相同的元素中v-for的优先级更高

Vue3.x中，v-if的优先级更高

### 17.v-bind

vue2.x中

```htaccess
<!-- template -->
<div id="red" v-bind="{ id: 'blue' }"></div>
<!-- result -->
<div id="red"></div>
```

vue3.x中

```html
<!-- template -->
<div id="red" v-bind="{ id: 'blue' }"></div>
<!-- result -->
<div id="blue"></div>

<!-- template -->
<div v-bind="{ id: 'blue' }" id="red"></div>
<!-- result -->
<div id="red"></div>

```

对于绑定相同的属性，v2不会覆盖，而v3会被覆盖 v3中绑定有顺序要求

### 18.v-on.native已经被移除

### 19.函数式组件

vue2.x中函数式组件主要用于性能优化，他的初始化速度要快于有状态组件，而且可以返回多个根节点，vue3.x中有状态组件的性能优化可以和无状态组件一样，而且还可以有多个跟节点

### 20.异步组件

vue2.x中异步组件的写法是

```js
const asyncModal = () => import('./Modal.vue')
```

或者可配置

```js
const asyncModal = {
  component: () => import('./Modal.vue'),
  delay: 200,
  timeout: 3000,
  error: ErrorComponent,
  loading: LoadingComponent
}
```

vue3中添加了一个定义异步组件的方法

```js
import { defineAsyncComponent } from 'vue'
import ErrorComponent from './components/ErrorComponent.vue'
import LoadingComponent from './components/LoadingComponent.vue'

// Async component without options
const asyncModal = defineAsyncComponent(() => import('./Modal.vue'))

// Async component with options
const asyncModalWithOptions = defineAsyncComponent({
  loader: () => import('./Modal.vue'),
  delay: 200,
  timeout: 3000,
  errorComponent: ErrorComponent,
  loadingComponent: LoadingComponent
})
```

而且vue2.x中的配置选项component变成了loader

vue3.x不再接受resolve，reject参数，而是返回一个promise

```js
// 2.x version
const oldAsyncComponent = (resolve, reject) => {
  /* ... */
}

// 3.x version
const asyncComponent = defineAsyncComponent(
  () =>
    new Promise((resolve, reject) => {
      /* ... */
    })
)
```

### 21.`emits` Option

vue2.x中不需要声明emits的值

```js
<template>
  <div>
    <p>{{ text }}</p>
    <button v-on:click="$emit('accepted')">OK</button>
  </div>
</template>
<script>
  export default {
    props: ['text']
  }
</script>
```

vue3.x中需要定义emits

```js
<template>
  <div>
    <p>{{ text }}</p>
    <button v-on:click="$emit('accepted')">OK</button>
  </div>
</template>
<script>
  export default {
    props: ['text'],
    emits: ['accepted']
  }
</script>
```

```js
下面例子中
<template>
  <button v-on:click="$emit('click', $event)">OK</button>
</template>
<script>
export default {
  emits: [] // without declared event
}
</script>

```

在父组件中监听click事件

```html
<my-button v-on:click="handleClick"></my-button>
```

click会被触发两次

### 22.$listener移除

### 23.$attrs中包含class和style

vue2.x中，`class`和`style`属性在 Vue 2 虚拟 DOM 实现中得到一些特殊处理。因此，它们*不*包含在 中`$attrs`，而所有其他属性都包含在 中。

使用时会出现这样的副作用`inheritAttrs: false`：

这样$attr中的属性不再自动绑定到子组件的根元素上，而是由开发者来决定怎么绑定他们，但是class和style不属于$attr的一部分，任然会应用在根元素上

```js
<template>
  <label>
    <input type="text" v-bind="$attrs" />
  </label>
</template>
<script>
export default {
  inheritAttrs: false
}
</script>
```

```js
<my-component id="my-id" class="my-class"></my-component>
```

真正渲染成的html

```js
<label class="my-class">
  <input type="text" id="my-id" />
</label>
```

在Vue3.x中则包含这些

### 24.v-on不再支持数字的keycode

### 25.不在支持$on,$off,$once

vue2.x中支持事件总线

```js
// eventBus.js

const eventBus = new Vue()

export default eventBus

```

```js
// ChildComponent.vue
import eventBus from './eventBus'

export default {
  mounted() {
    // adding eventBus listener
    eventBus.$on('custom-event', () => {
      console.log('Custom event triggered!')
    })
  },
  beforeDestroy() {
    // removing eventBus listener
    eventBus.$off('custom-event')
  }
}

```

```js
// ParentComponent.vue
import eventBus from './eventBus'

export default {
  methods: {
    callGlobalCustomEvent() {
      eventBus.$emit('custom-event') // if ChildComponent is mounted, we will have a message in the console
    }
  }
}

```

vue3.x中事件总线可以使用第三方的库来实现，例如[mitt](https://github.com/developit/mitt)或[tiny-emitter](https://github.com/scottcorgan/tiny-emitter)

```js
// eventBus.js
import emitter from 'tiny-emitter/instance'

export default {
  $on: (...args) => emitter.on(...args),
  $once: (...args) => emitter.once(...args),
  $off: (...args) => emitter.off(...args),
  $emit: (...args) => emitter.emit(...args)
}

```

### 26.filters

vue3.x移除了过滤器，可以使用methods和computed来替代

全局的过滤器可以使用如下方式来在vue3中定义

```js
// main.js
const app = createApp(App)

app.config.globalProperties.$filters = {
  currencyUSD(value) {
    return '$' + value
  }
}

```

```js
<template>
  <h1>Bank Account Balance</h1>
  <p>{{ $filters.currencyUSD(accountBalance) }}</p>
</template>

```

### 27.删除了inline-template

### 28.删除了$children

vue2.x可以使用$children来访问直接子组件，vue3.x使用refs来替代就可以了

### 29.自定义指令

指令的钩子函数已经重新命名，更好的和组件的生命周期配合，此外，`expression`字符串不再作为`binding`对象的一部分传递

vue2.x中的directive的可选项

bind  指令只绑定一次到元素中

inserted  元素只被插入父元素中一次

update  该hook表示当元素更新时，children还没有更新

componentUpdated  表示组件和子元素都更新完毕

unbind 表示解绑

vue2.x的自定义指令

```html
<p v-highlight="'yellow'">Highlight this text bright yellow</p>

```

```java
Vue.directive('highlight', {
  bind(el, binding, vnode) {
    el.style.background = binding.value
  }
})

```

vue3.x中新增了created    这个声明周期是在元素的属性和事件被应用之前

bind ->beforeMount

inserted ->mounted

beforeUpdate 类似于组件的beforeUpdate

update被移除

componentUpdated → **updated**

**beforeUnmount**: new! Similar to component lifecycle hooks, this will be called right before an element is unmounted

unbind -> **unmounted**

```html
const MyDirective = {
  created(el, binding, vnode, prevVnode) {}, // new
  beforeMount() {},
  mounted() {},
  beforeUpdate() {}, // new
  updated() {},
  beforeUnmount() {}, // new
  unmounted() {}
}

```

```html
<p v-highlight="'yellow'">Highlight this text bright yellow</p>

```

```html
const app = Vue.createApp({})

app.directive('highlight', {
  beforeMount(el, binding, vnode) {
    el.style.background = binding.value
  }
})

```

### 30.data option 

vue2.x中还可以使用对象作为data的值。vue3.x不可以使用对象作为data,必须使用function

### 31.元素的挂载

在vue2.x中当挂载的元素是一个templayte的时候，渲染的内容会替换掉需要挂载的元素，在vue3.x中，需要被渲染的内容会添加到元素的children中，替代元素的innerHTML

vue2.x中

```html
new Vue({
  el: '#app',
  data() {
    return {
      message: 'Hello Vue!'
    }
  },
  template: `
    <div id="rendered">{{ message }}</div>
  `
})

// or
const app = new Vue({
  data() {
    return {
      message: 'Hello Vue!'
    }
  },
  template: `
    <div id="rendered">{{ message }}</div>
  `
})

app.$mount('#app')
```

挂载到对应的元素上

```html
<body>
  <div id="app">
    Some app content
  </div>
</body>
```

最终渲染的效果

```html
<body>
  <div id="rendered">Hello Vue!</div>
</body>
```

vue3.x中的效果

```html
const app = Vue.createApp({
  data() {
    return {
      message: 'Hello Vue!'
    }
  },
  template: `
    <div id="rendered">{{ message }}</div>
  `
})

app.mount('#app')

```

```html
<body>
  <div id="app" data-v-app="">
    <div id="rendered">Hello Vue!</div>
  </div>
</body>

```

### 32.transition的变化

.v-enter已被替换成.v-enter-from

### 33.vNode 生命周期事件

在vue2.x中，可以使用事件来监听组件生命周期处于哪个阶段，这些事件的前缀是hook:,在 Vue 3 中，此前缀已更改为`vue:`. 此外，这些事件现在可用于 HTML 元素和组件

vue2.x 

hook:后面跟的是生命周期函数的名称

```html
<template>
  <child-component @hook:updated="onUpdated">
</template>
```

vue3.x中

```html
<template>
  <child-component @vue:updated="onUpdated">
</template>
```

### 34.数组的监听

vue3.x中，数组的监听需要加上deep

```html
watch: {
  bookList: {
    handler(val, oldVal) {
      console.log('book list changed')
    },
    deep: true
  },
}
```

