---
title: Vue3笔记(二)
date: 2022-08-18 13:51:27
tags:
categories:
- [vue, note]
---

#### 64.异步组件

vue2中的异步组件是使用import().then()来实现的

vue3中提供了函数defineAsyncComponent来实现异步组件，这这样可以对组件进行分包处理，不会打包到一个文件中，而是单独打到一个包中

```js
//方法一 因为这种方式也会返回一个promise
const AsyncCategory = defineAsyncComponent(() => import("./views/Category.vue"))
```

```js
//方法二
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...从服务器获取组件
    resolve(/* 获取到的组件 */)
  })
})
// ... 像使用其他一般组件一样使用 `AsyncComp`
```

`defineAsyncComponent` 方法接收一个返回 Promise 的加载函数。这个 Promise 的 `resolve` 回调方法应该在从服务器获得组件定义时调用。你也可以调用 `reject(reason)` 表明加载失败。

全局注册方式

```js
app.component('MyComponent', defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
))
```

defineAsyncComponent的高级选项

```js
const AsyncComp = defineAsyncComponent({
  // 加载函数
  loader: () => import('./Foo.vue'),

  // 加载异步组件时使用的组件
  loadingComponent: LoadingComponent,
  // 展示加载组件前的延迟时间，默认为 200ms
  delay: 200,

  // 加载失败后展示的组件
  errorComponent: ErrorComponent,
  // 如果提供了一个 timeout 时间限制，并超时了
  // 也会显示这里配置的报错组件，默认值是：Infinity
  timeout: 3000
})
```

#### 65.组件中的v-model

可以对组件进行双向数据绑定

1. 将内部原生 `input` 元素的 `value` attribute 绑定到 `modelValue` prop
2. 输入新的值时在 `input` 元素上触发 `update:modelValue` 事件

```js
//App.js
<CustomInput
  :modelValue="searchText"
  @update:modelValue="newValue => searchText = newValue"
/>
```

```js
<!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue']
}
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

```js
<CustomInput v-model="searchText" />//这是上面的简写 v-model就可以使用了
```

方式二。使用computed来实现v-model

```js
<!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
}
</script>

<template>
  <input v-model="value" />
</template>
```

v-model的参数是可以修改的

```js
<MyComponent v-model:title="bookTitle" />  //在组件中修改props的值还有emits的值为title和update:title
```

###### 多个 `v-model` 绑定

```js
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

```js
<script>
export default {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName']
}
</script>

<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>
```

###### 处理 `v-model` 修饰符

我们来创建一个自定义的修饰符 `capitalize`，它会自动将 `v-model` 绑定输入的字符串值第一个字母转为大写：

```js
<MyComponent v-model.capitalize="myText" />

```

组件的 `v-model` 上所添加的修饰符，可以通过 `modelModifiers` prop 在组件内访问到。在下面的组件中，我们声明了 `modelModifiers` 这个 prop，它的默认值是一个空对象

```js
<script>
export default {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  created() {
    console.log(this.modelModifiers) // { capitalize: true }
  }
}
</script>

<template>
  <input
    type="text"
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

注意这里组件的 `modelModifiers` prop 包含了 `capitalize` 且其值为 `true`，因为它在模板中的 `v-model` 绑定上被使用了。

有了 `modelModifiers` 这个 prop，我们就可以在原生事件侦听函数中检查它的值，然后决定触发的自定义事件中要向父组件传递什么值。在下面的代码里，我们就是在每次 `<input>` 元素触发 `input` 事件时将值的首字母大写：

```js
<script>
export default {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  methods: {
    emitValue(e) {
      let value = e.target.value
      if (this.modelModifiers.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }
      this.$emit('update:modelValue', value)
    }
  }
}
</script>

<template>
  <input type="text" :value="modelValue" @input="emitValue" />
</template>
```

对于又有参数又有修饰符的 `v-model` 绑定，生成的 prop 名将是 `arg + "Modifiers"`。举例来说：

```js
<MyComponent v-model:title.capitalize="myText">

```

```js
export default {
  props: ['title', 'titleModifiers'],
  emits: ['update:title'],
  created() {
    console.log(this.titleModifiers) // { capitalize: true }
  }
}
```

#### 66.mixins

局部混入的写法

```
const mixin = {
  created() {
    console.log(1)
  }
}

createApp({
  created() {
    console.log(2)
  },
  mixins: [mixin]
})

// => 1
// => 2
```

Mixin 钩子的调用顺序与提供它们的选项顺序相同，且会在组件自身的钩子前被调用

在 Vue 2 中，mixins 是创建可重用组件逻辑的主要方式。尽管在 Vue 3 中保留了 mixins 支持，但对于组件间的逻辑复用，[Composition API](https://cn.vuejs.org/guide/reusability/composables.html) 是现在更推荐的方式。

app.mixin()全局的混入 但是vue3不推荐使用

#### 67.setup()函数

setup函数有两个参数：props,context,在setup函数里面不能使用this

context参数包含3个属性：

attrs：所有非props的attribute

slots:父组件传递过来的插槽

emit

```js
export default {
  setup(props, context) {
    // 透传 Attributes（非响应式的对象，等价于 $attrs）
    console.log(context.attrs)

    // 插槽（非响应式的对象，等价于 $slots）
    console.log(context.slots)

    // 触发事件（函数，等价于 $emit）
    console.log(context.emit)

    // 暴露公共属性（函数）
    console.log(context.expose)
  }
}
```

context是非响应性的，可以进行解构

```js
export default {
  setup(props, { attrs, slots, emit, expose }) {
    ...
  }
}
```

`attrs` 和 `slots` 都是有状态的对象，它们总是会随着组件自身的更新而更新。这意味着你应当避免解构它们，并始终通过 `attrs.x` 或 `slots.x` 的形式使用其中的属性。此外还需注意，和 `props` 不同，`attrs` 和 `slots` 的属性都**不是**响应式的。如果你想要基于 `attrs` 或 `slots` 的改变来执行副作用，那么你应该在 `onBeforeUpdate` 生命周期钩子中编写相关逻辑。

setup可以有返回值，可以在template中使用

`setup` 函数的第一个参数是组件的 `props`。和标准的组件一致，一个 `setup` 函数的 `props` 是响应式的，并且会在传入新的 props 时同步更新。

```js
export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```

如果解构props会使数据失去响应性，如果需要结构请使用toRef()/toRefs()

```js
import { toRefs, toRef } from 'vue'

export default {
  setup(props) {
    // 将 `props` 转为一个其中全是 ref 的对象，然后解构
    const { title } = toRefs(props)
    // `title` 是一个追踪着 `props.title` 的 ref
    console.log(title.value)

    // 或者，将 `props` 的单个属性转为一个 ref
    const title = toRef(props, 'title')
  }
}
```

请注意在模板中访问从 `setup` 返回的 [ref](https://cn.vuejs.org/api/reactivity-core.html#ref) 时，它会[自动浅层解包](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals.html#deep-reactivity)，因此你无须再在模板中为它写 `.value`。当通过 `this` 访问时也会同样如此解包。

**TIP**

**`setup()` 自身并不含对组件实例的访问权，即在 `setup()` 中访问 `this` 会是 `undefined`。你可以在选项式 API 中访问组合式 API 暴露的值，但反过来则不行。**

##### 暴露公共属性

`expose` 函数用于显式地限制该组件暴露出的属性，当父组件通过[模板引用](https://cn.vuejs.org/guide/essentials/template-refs.html#ref-on-component)访问该组件的实例时，将仅能访问 `expose` 函数暴露出的内容：

```js
export default {
  setup(props, { expose }) {
    // 让组件实例处于 “关闭状态”
    // 即不向父组件暴露任何东西
    expose()

    const publicCount = ref(0)
    const privateCount = ref(0)
    // 有选择地暴露局部状态
    expose({ count: publicCount })
  }
}
```

##### 与渲染函数一起使用[#](https://cn.vuejs.org/api/composition-api-setup.html#usage-with-render-functions)

`setup` 也可以返回一个[渲染函数](https://cn.vuejs.org/guide/extras/render-function.html)，此时在渲染函数中可以直接使用在同一作用域下声明的响应式状态：

```
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return () => h('div', count.value)
  }
}
```

```js
import { h, ref } from 'vue'

export default {
  setup(props, { expose }) {
    const count = ref(0)
    const increment = () => ++count.value

    expose({
      increment
    })

    return () => h('div', count.value)
  }
}
```

#### 68. reactive api

reactive可以将对象变成响应式对象  ，返回一个对象的响应式代理

```
  const account = reactive({
        username: "coderwhy",
        password: "1234567"
      })
```

reactive转换是深层的，他会影响所有的嵌套属性，reactive也会深层的解包所有ref属性，同时保持响应性

值得注意的是，当访问到某个响应式数组或 `Map` 这样的原生集合类型中的 ref 元素时，不会执行 ref 的解包。意思是

若要避免深层响应式转换，只想保留对这个对象顶层次访问的响应性，请使用 [shallowReactive()](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive) 作替代

返回的对象以及其中嵌套的对象都会通过 [ES Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 包裹，因此**不等于**源对象，建议只使用响应式代理，避免使用原始对象。

ref 的解包：

```js
const count = ref(1)
const obj = reactive({ count })

// ref 会被解包
console.log(obj.count === count.value) // true

// 会更新 `obj.count`
count.value++
console.log(count.value) // 2
console.log(obj.count) // 2

// 也会更新 `count` ref
obj.count++
console.log(obj.count) // 3
console.log(count.value) // 3
```

注意当访问到某个响应式数组或 `Map` 这样的原生集合类型中的 ref 元素时，**不会**执行 ref 的解包：

```js
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// 这里需要 .value
console.log(map.get('count').value)
```

将一个 [ref](https://cn.vuejs.org/api/reactivity-core.html#ref) 赋值给为一个 `reactive` 属性时，该 ref 会被自动解包：

```js
const count = ref(1)
const obj = reactive({})

obj.count = count

console.log(obj.count) // 1
console.log(obj.count === count.value) // true
```

#### 69.ref api

ref 可以定义任何数据类型

ref会返回一个可变的响应式对象，该对象作为一个响应式的引用维护它内部的值,

```
 const info = ref({})
 console.log(info.value)
```

在模板中使用ref的值，vue会自动帮助我们进行解包不需要使用.value,但是在setup函数内部，还是一个ref引用，必须使用.value的方式。在模板中的解包是浅层的解包

```
// 2.定义从网络中获取的数据也是使用ref
      // const musics = reactive([])
      const musics = ref([])
      onMounted(() => {
        const serverMusics = ["海阔天空", "小苹果", "野狼"]
        musics.value = serverMusics
      })
```

#### 70.readonly()

传入一个对象，无论是响应式还是普通对象，还是ref对象，会返回一个对象的只读代理

可以用于给子组件传递数据的时候，不允许修改父组件传递的值，，readonly他会劫持proxy中的set方法，不允许修改

只读代理是深层的：对任何嵌套属性的访问都将是只读的。它的 ref 解包行为与 `reactive()` 相同，但解包得到的值是只读的。

```js
const original = reactive({ count: 0 })

const copy = readonly(original)

watchEffect(() => {
  // 用来做响应性追踪
  console.log(copy.count)
})

// 更改源属性会触发其依赖的侦听器
original.count++

// 更改该只读副本将会失败，并会得到一个警告
copy.count++ // warning!
```

其实本质上就是readonly返回的对象的setter方法被劫持了而已

#### 71.reactive判断的api

##### isProxy

​	检查对象是否是由reactive或者readonly创建的proxy

##### isReactive

检查一个对象是否是由 [`reactive()`](https://cn.vuejs.org/api/reactivity-core.html#reactive) 或 [`shallowReactive()`](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive) 创建的代理

如果该代理是readonly创建的，但是包裹了由reactive创建的另外一个代理，他也会返回true

##### isReadonly

检查一个对象是否是由 [`readonly()`](https://cn.vuejs.org/api/reactivity-core.html#readonly) 或 [`shallowReadonly()`](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreadonly) 创建的代理。

##### toRow

 返回 reactive 或 readonly 代理的原始对象（不建议保留对原始对象的持久引用。请谨慎使用）。

#####  shallowReactive

创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换 (深层还是原生对象)。

##### shallowReadonly

创建一个 proxy，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换（深层还是可读、可写的）

#### 72 toRefs

如果使用es6的解构语法对reactive返回的对象进行解构，解构后获得的变量不是响应式的，可以使用toRefs将解构后的值变成响应式的

```js
 const info = reactive({
          name: "why",
          age: 18,
          height: 1.88
        })

        // reactive被解构后会变成普通的值, 失去响应式
        const { name, age } = toRefs(info)
```

`toRefs` 在调用时只会为源对象上可以枚举的属性创建 ref。如果要为可能还不存在的属性创建 ref，请改用 [`toRef`](https://cn.vuejs.org/api/reactivity-utilities.html#toref)。

#### 73.toRef

将一个reactive对象的属性变成响应式的

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const fooRef = toRef(state, 'foo')

// 更改该 ref 会更新源属性
fooRef.value++
console.log(state.foo) // 2

// 更改源属性也会更新该 ref
state.foo++
console.log(fooRef.value) // 3
```

请注意，这不同于：

```js
const fooRef = ref(state.foo)
```

上面这个 ref **不会**和 `state.foo` 保持同步，因为这个 `ref()` 接收到的是一个纯数值。

#### 74. ref其他api

##### unref

如果我们想要获取一个ref引用中的value，那么也可以通过unref方法：  如果参数是一个 ref，则返回内部值，否则返回参数本身；  这是 val = isRef(val) ? val.value : val 的语法糖函数

##### isRef

判断值是否是一个ref对象。

##### shallowRef

创建一个浅层的ref对象

##### triggerRef

手动触发和 shallowRef 相关联的副作用：

#### 75.computed()

接受一个 getter 函数，返回一个只读的响应式 [ref](https://cn.vuejs.org/api/reactivity-core.html#ref) 对象。该 ref 通过 `.value` 暴露 getter 函数的返回值。它也可以接受一个带有 `get` 和 `set` 函数的对象来创建一个可写的 ref 对象。

```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)

console.log(plusOne.value) // 2

plusOne.value++ // 错误
```

```js
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0
```

#### 76.在setup中获取组件实例

```js
<show-info ref="showInfoRef"></show-info>

setup(){
	const showInfoRef=ref()
	return {
		showInfoRef
	}
}
```

#### 77.在setup中使用生命周期钩子函数

onBeforeMount

onMounted

onBeforeUpdate

onUpdated

onBeforeUnmounted

onUnounted

onActivited

onDeactivited

#### 78.provide

- 与注册生命周期钩子的 API 类似，`provide()` 必须在组件的 `setup()` 阶段同步调用。

  ```js
  <script setup>
  import { ref, provide } from 'vue'
  import { fooSymbol } from './injectionSymbols'
  
  // 提供静态值
  provide('foo', 'bar')
  
  // 提供响应式的值
  const count = ref(0)
  provide('count', count)
  
  // 提供时将 Symbol 作为 key
  provide(fooSymbol, count)
  </script>
  ```

#### 79.inject()

第一个参数是注入的 key。Vue 会遍历父组件链，通过匹配 key 来确定所提供的值。如果父组件链上多个组件对同一个 key 提供了值，那么离得更近的组件将会“覆盖”链上更远的组件所提供的值。如果没有能通过 key 匹配到值，`inject()` 将返回 `undefined`，除非提供了一个默认值。

第二个参数是可选的，即在没有匹配到 key 时使用的默认值。它也可以是一个工厂函数，用来返回某些创建起来比较复杂的值。如果默认值本身就是一个函数，那么你必须将 `false` 作为第三个参数传入，表明这个函数就是默认值，而不是一个工厂函数。

与注册生命周期钩子的 API 类似，`inject()` 必须在组件的 `setup()` 阶段同步调用。

```js
<script setup>
import { inject } from 'vue'
import { fooSymbol } from './injectionSymbols'

// 注入值的默认方式
const foo = inject('foo')

// 注入响应式的值
const count = inject('count')

// 通过 Symbol 类型的 key 注入
const foo2 = inject(fooSymbol)

// 注入一个值，若为空则使用提供的默认值
const bar = inject('foo', 'default value')

// 注入一个值，若为空则使用提供的工厂函数
const baz = inject('foo', () => new Map())

// 注入时为了表明提供的默认值是个函数，需要传入第三个参数
const fn = inject('function', () => {}, false)
</script>
```

#### 80.watch()

watch的api和option API中的watch类似

watch需要监听特定的数据源，并且执行其回调函数，默认情况下，他是惰性的，在初始化情况下不执行，只有在数据变化时才执行

`watch()` 默认是懒侦听的，即仅在侦听源发生变化时才执行回调函数。

第一个参数是侦听器的**源**。这个来源可以是以下几种：

- 一个函数，返回一个值
- 一个 ref
- 一个响应式对象
- ...或是由以上类型的值组成的数组

第二个参数是在发生变化时要调用的回调函数。这个回调函数接受三个参数：新值、旧值，以及一个用于注册副作用清理的回调函数。该回调函数会在副作用下一次重新执行前调用，可以用来清除无效的副作用，例如等待中的异步请求。

当侦听多个来源时，回调函数接受两个数组，分别对应来源数组中的新值和旧值。

第三个可选的参数是一个对象，支持以下这些选项：

- **`immediate`**：在侦听器创建时立即触发回调。第一次调用时旧值是 `undefined`。

- **`deep`**：如果源是对象，强制深度遍历，以便在深层级变更时触发回调。参考[深层侦听器](https://cn.vuejs.org/guide/essentials/watchers.html#deep-watchers)一节。

- **`flush`**：调整回调函数的刷新时机。参考[回调的刷新时机](https://cn.vuejs.org/guide/essentials/watchers.html#callback-flush-timing)一节。

- **`onTrack / onTrigger`**：调试侦听器的依赖,参考[调试侦听器](https://cn.vuejs.org/guide/extras/reactivity-in-depth.html#watcher-debugging)一节。

  

与 [`watchEffect()`](https://cn.vuejs.org/api/reactivity-core.html#watcheffect) 相比，`watch()` 使我们可以：

- 懒执行副作用；
- 更加明确是应该由哪个状态触发侦听器重新执行；
- 可以访问所侦听状态的前一个值和当前值。

侦听一个 getter 函数：

```js
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)
```

侦听一个 ref：

```js
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```

当侦听多个来源时，回调函数接受两个数组，分别对应来源数组中的新值和旧值：

```js
watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* ... */
})
```

当使用 getter 函数作为源时，回调只在此函数的返回值变化时才会触发。如果你想让回调在深层级变更时也能触发，你需要使用 `{ deep: true }` 强制侦听器进入深层级模式。在深层级模式时，如果回调函数由于深层级的变更而被触发，那么新值和旧值将是同一个对象。

```js
const state = reactive({ count: 0 })
watch(
  () => state,
  (newValue, oldValue) => {
    // newValue === oldValue
  },
  { deep: true,immediate:true//在创建的时候执行一次 }
)
```

当直接侦听一个响应式对象时，侦听器会自动启用深层模式：

```js
const state = reactive({ count: 0 })
watch(state, () => {
  /* 深层级变更状态所触发的回调 */
})
```

`watch()` 和 [`watchEffect()`](https://cn.vuejs.org/api/reactivity-core.html#watcheffect) 享有相同的刷新时机和调试选项：

```js
watch(source, callback, {
  flush: 'post',
  onTrack(e) {
    debugger
  }
})
```

#### 81.watchEffect()

立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行。

- 第一个参数就是要运行的副作用函数。这个副作用函数的参数也是一个函数，用来注册清理回调。清理回调会在该副作用下一次执行前被调用，可以用来清理无效的副作用，例如等待中的异步请求 (参见下面的示例)。

  第二个参数是一个可选的选项，可以用来调整副作用的刷新时机或调试副作用的依赖。

  返回值是一个用来停止该副作用的函数。

- **示例**

  ```js
  const count = ref(0)
  
  watchEffect(() => console.log(count.value))
  // -> 输出 0
  
  count.value++
  // -> 输出 1
  ```

  副作用清除：

  ```js
  watchEffect(async (onCleanup) => {
    const { response, cancel } = doAsyncWork(id.value)
    // `cancel` 会在 `id` 更改时调用
    // 以便取消之前
    // 未完成的请求
    onCleanup(cancel)
    data.value = await response
  })
  ```

  停止侦听器：

  ```js
  const stop = watchEffect(() => {})
  
  // 当不再需要此侦听器时:
  stop()
  ```

  选项：

  ```js
  watchEffect(() => {}, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

#### 82.hooks的封装

封装usetitle

```js
//useTitle.js
import { ref, watch } from "vue";
//这是闭包函数
export default function useTitle(titleValue) {
  // document.title = title

  // 定义ref的引入数据
  const title = ref(titleValue)

  // 监听title的改变
  watch(title, (newValue) => {
    document.title = newValue
  }, {
    immediate: true
  })

  // 返回ref值
  return {
    title
  }
}
```

```js
 //App.vue
// 2.修改标题
      const { title } = useTitle("首页")

      // 3.监听按钮的点击  这样修改的是同一个值，不需要重复的调用usetitle("首页-流行")
      function popularClick() {
        title.value = "首页-流行"
      }
      function hotClick() {
        title.value = "首页-热门"
      }
      function songClick() {
        title.value = "首页-歌单"
      }
```

#### 84.单文件组件 `<script setup>`

<script setup> 是在单文件组件 (SFC) 中使用组合式 API 的编译时语法糖。

- 更少的样板内容，更简洁的代码。
- 能够使用纯 TypeScript 声明 props 和自定义事件。
- 更好的运行时性能 (其模板会被编译成同一作用域内的渲染函数，避免了渲染上下文代理对象)。
- 更好的 IDE 类型推导性能 (减少了语言服务器从代码中抽取类型的工作)。

```js
<script setup>
console.log('hello script setup')
</script>
```

与普通的 `<script>` 只在组件被首次引入的时候执行一次不同，`<script setup>` 中的代码会在**每次组件实例被创建的时候执行**。响应式数据需要使用ref或者reactive来实现

##### 顶层的绑定会被暴露给模板[#](https://cn.vuejs.org/api/sfc-script-setup.html#top-level-bindings-are-exposed-to-template)

当使用 `<script setup>` 的时候，任何在 `<script setup>` 声明的顶层的绑定 (包括变量，函数声明，以及 import 导入的内容) 都能在模板中直接使用：

```
<script setup>
// 变量
const msg = 'Hello!'

// 函数
function log() {
  console.log(msg)
}
</script>

<template>
  <button @click="log">{{ msg }}</button>
</template>
```

import 导入的内容也会以同样的方式暴露。这意味着我们可以在模板表达式中直接使用导入的 helper 函数，而不需要通过 `methods` 选项来暴露它：

```
<script setup>
import { capitalize } from './helpers'
</script>

<template>
  <div>{{ capitalize('hello') }}</div>
</template>
```

##### 组件的使用

组件直接导入即可，不需要注册

```
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

##### **`defineProps()`**，`defineEmits（）`

用于在`<script setup>`中定义props和emits

```
<script setup>

// 定义props
const props = defineProps({
  name: {
    type: String,
    default: "默认值"
  },
  age: {
    type: Number,
    default: 0
  }
})

// 绑定函数, 并且发出事件
const emits = defineEmits(["infoBtnClick"])
function showInfoBtnClick() {
  emits("infoBtnClick", "showInfo内部发生了点击")
}

// 定义foo的函数
function foo() {
  console.log("foo function")
}
defineExpose({
  foo
})

</script>
```

##### `defineExpose`

使用 `<script setup>` 的组件是**默认关闭**的——即通过模板引用或者 `$parent` 链获取到的组件的公开实例，**不会**暴露任何在 `<script setup>` 中声明的绑定。

可以通过 `defineExpose` 编译器宏来显式指定在 `<script setup>` 组件中要暴露出去的属性：

```
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```
