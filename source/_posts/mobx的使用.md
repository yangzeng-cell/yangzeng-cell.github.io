---
title: mobx的使用
date: 2022-09-20 22:40:53
tags:
categories:
- [react,mobx]
---

## 入门

*任何可以从应用状态中派生出来的值都应该被自动派生出来。*

MobX 是一个身经百战的库，它通过运用透明的函数式响应编程（Transparent Functional Reactive Programming，TFRP）使状态管理变得简单和可扩展。

😙

##### 简单直接

编写无模板的极简代码来精准描述出你的意图。要更新一个记录字段？使用熟悉的 JavaScript 赋值就行。要在异步进程中更新数据？不需要特殊的工具，响应性系统会侦测到你所有的变更并把它们传送到其用武之地。

🚅

##### 轻松实现最优渲染

所有对数据的变更和使用都会在运行时被追踪到，并构成一个截取所有状态和输出之间关系的依赖树。这样保证了那些依赖于状态的计算只有在真正需要时才会运行，就像 React 组件一样。无需使用记忆化或选择器之类容易出错的次优技巧来对组件进行手动优化。

🤹🏻‍♂️

##### 架构自由

MobX 不会用它自己的规则来限制你，它可以让你在任意 UI 框架之外管理你的应用状态。这样会使你的代码低耦合、可移植和最重要的——容易测试。

```js
import React from "react"
import ReactDOM from "react-dom"
import { makeAutoObservable } from "mobx"
import { observer } from "mobx-react"

// 对应用状态进行建模。
class Timer {
    secondsPassed = 0

    constructor() {
        makeAutoObservable(this)
    }

    increase() {
        this.secondsPassed += 1
    }

    reset() {
        this.secondsPassed = 0
    }
}

const myTimer = new Timer()

// 构建一个使用 observable 状态的“用户界面”。
const TimerView = observer(({ timer }) => (
    <button onClick={() => timer.reset()}>已过秒数：{timer.secondsPassed}</button>
))

ReactDOM.render(<TimerView timer={myTimer} />, document.body)

// 每秒更新一次‘已过秒数：X’中的文本。
setInterval(() => {
    myTimer.increase()
}, 1000)
```

围绕 React 组件 `TimerView` 的 `observer` 包装会自动侦测到依赖于 observable `timer.secondsPassed` 的渲染——即使这种依赖关系没有被明确定义出来。 响应性系统会负责在未来*恰好那个*字段被更新的时候将组件重新渲染。

每个事件（`onClick` 或 `setInterval`）都会调用一个用来更新 *observable 状态* `myTimer.secondsPassed` 的 *action*（`myTimer.increase` 或 `myTimer.reset`）。Observable 状态的变更会被精确地传送到 `TimerView` 中所有依赖于它们的*计算*和*副作用*里。

![MobX 单向流](https://zh.mobx.js.org/assets/zh.flow.png)

# MobX 主旨

Mobx区分了应用程序的三个概念

- State
- Actions
- Derivations

### state

*State(状态)* 是驱动你的应用程序的数据。

```js
import { makeObservable, observable, action } from "mobx"

class Todo {
    id = Math.random()
    title = ""
    finished = false

    constructor(title) {
        makeObservable(this, {
            title: observable,
            finished: observable,
            toggle: action
        })
        this.title = title
    }

    toggle() {
        this.finished = !this.finished
    }
}
```

将 State 存储在任何您喜欢的数据结构中：普通对象、数组、类、循环数据结构或引用。这与MobX的工作方式无关。

只要确保所有响应式的属性都被标记为`observable`

### Action

action是用来改变state中的数据，比如用户事件处理、后端推送数据处理、调度器事件处理等等。

### 创建 Derivations 以便自动对 State 变化进行响应

*任何* 来源是*State(状态)* 并且不需要进一步交互的东西都是 Derivation(派生)

Derivations 包括许多方式:

- *用户界面*
- *派生数据* , 比如剩余未完成`todos`的数量
- *后端集成* , 比如发送改变到服务器端

Mobx 区分了两种 Derivation :

- *Computed values*,总是可以通过纯函数从当前的可观测 State 中派生。
- *Reactions*, 当 State 改变时需要自动运行的副作用 (命令式编程和响应式编程之间的桥梁)

当最开始使用MobX时，人们容易过度使用 *Reaction*。

黄金法则是，如果要基于当前 State 创建值，请始终使用 *computed*

#### 通过 computed 对派生值进行建模

你可以通过定义 getter 方法并使用 `makeObservable` 将其标记为 `computed` 的方式创建一个 *computed* 值。

```js
import { makeObservable, observable, computed } from "mobx"

class TodoList {
    todos = []
    get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length
    }
    constructor(todos) {
        makeObservable(this, {
            todos: observable,
            unfinishedTodoCount: computed
        })
        this.todos = todos
    }
}
```

Mobx 会确保 `unfinishedTodoCount` 会在todos数组发生变化中或者 todos中的一个对象中的 `finished`属性被修改时自动更新

#### Reaction会产生副作用

作为用户，要想在屏幕上看到状态或计算值的变化，就需要一个重新绘制部分GUI的 *reactions* 。

Reaction 和 computed 类似，但并不产生信息，而是产生副作用，如打印到控制台、发出网络请求、增量更新 React 组件树以便更新DOM等。

简而言之，*reaction* 是 [响应式编程](https://en.wikipedia.org/wiki/Reactive_programming)和[指令式编程](https://en.wikipedia.org/wiki/Imperative_programming)之间的桥梁。

到目前为止，最常用的 reaction 形式是UI组件。 注意，action 和 reaction 都可能引起副作用。 副作用应有一个清晰的、显式的起源，例如在提交表单时发出网络请求，应该从相关的事件处理程序显式触发。

## 原则

Mobx 使用单向数据流，利用 *action* 改变 *state* ，进而更新所有受影响的 *view*

![Action, State, View](https://zh.mobx.js.org/assets/action-state-view.png)

1. 所有的 *derivations* 将在 *state* 改变时**自动且原子化地更新**。因此不可能观察中间值。
2. 所有的 *derivations* 默认将会**同步**更新，这意味着 *action* 可以在 *state* 改变 之后安全的直接获得 computed 值。
3. *computed value* 的更新是**惰性**的，任何 computed value 在需要他们的副作用发生之前都是不激活的。
4. 所有的 *computed value* 都应是**纯函数**,他们不应该修改 *state*。



## Observable state

属性，完整的对象，数组，Maps 和 Sets 都可以被转化为可观察对象。 使得对象可观察的基本方法是使用 `makeObservable` 为每个属性指定一个注解。 最重要的注解如下：

- `observable` 定义一个存储 state 的可追踪字段。
- `action` 将一个方法标记为可以修改 state 的 action。
- `computed` 标记一个可以由 state 派生出新的值并且缓存其输出的 getter。

像数组，Maps 和 Sets 这样的集合都将被自动转化为可观察对象

#### `makeObservable`

用法：

- `makeObservable(target, annotations?, options?)`

这个函数会将已存在的对象属性转换成可观察地对象，任何target对象包括class都可以作为target传递。一般情况下，`makeObservable` 是在类的构造函数中调用的，并且它的第一个参数是 `this` 。 `annotations` 参数将会为每一个成员映射 [注解](https://zh.mobx.js.org/observable-state.html#可用的注解)。需要注意的是，当使用 [装饰器](https://zh.mobx.js.org/enabling-decorators.html) 时，`annotations` 参数将会被忽略。

```js
import { makeObservable, observable, computed, action, flow } from "mobx"

class Doubler {
    value

    constructor(value) {
        makeObservable(this, {
            value: observable,
            double: computed,
            increment: action,
            fetch: flow
        })
        this.value = value
    }

    get double() {
        return this.value * 2
    }

    increment() {
        this.value++
    }

    *fetch() {
        const response = yield fetch("/api/value")
        this.value = response.json()
    }
}
所有带注解 的字段都是 不可配置的。
所有的不可观察（无状态）的字段（action, flow）都是 不可写的。
```

#### `makeAutoObservable`

使用：

- `makeAutoObservable(target, overrides?, options?)`

相当于是加强版的makeObservable函数，他会自动根据传入的对象属性自动转成可观察属性，

与使用 `makeObservable` 相比，`makeAutoObservable` 函数更紧凑，也更容易维护，因为新成员不需要显式地提及。 然而，`makeAutoObservable` 不能被用于带有 super 的类或 [子类](https://zh.mobx.js.org/subclassing.html)

推断规则：

- 所有 *自有* 属性都成为 `observable`。
- 所有 `get`ters 都成为 `computed`。
- 所有 `set`ters 都成为 `action`。
- 所有 *prototype 中的 functions* 都成为 `autoAction`。
- 所有 *prototype 中的 generator functions* 都成为 `flow`。（需要注意，generators 函数在某些编译器配置中无法被检测到，如果 flow 没有正常运行，请务必明确地指定 `flow` 注解。）
- 在 `overrides` 参数中标记为 `false` 的成员将不会被添加注解。例如，将其用于像标识符这样的只读字段。

#### `observable`

用法：

- `observable(source, overrides?, options?)`

`observable` 注解可以作为一个函数进行调用，从而一次性将整个对象变成可观察的。 `source` 对象将会被克隆并且所有的成员都将会成为可观察的，类似于 `makeAutoObservable` 做的那样。 同样，你可以传入一个 `overrides` 对象来为特定的成员提供特定的注解。 

由 `observable` 返回的对象将会使用 Proxy 包装，这意味着之后被添加到这个对象中的属性也将被侦测并使其转化为可观察对象（除非禁用 [proxy](https://zh.mobx.js.org/configuration.html#proxy-选项)）。

`observable` 方法也可以被像 [arrays](https://zh.mobx.js.org/api.html#observablearray)，[Maps](https://zh.mobx.js.org/api.html#observablemap) 和 [Sets](https://zh.mobx.js.org/api.html#observableset) 这样的集合调用。这些集合也将被克隆并转化为可观察对象。

```js
import { observable, autorun } from "mobx"

const todos = observable([
    { title: "Spoil tea", completed: true },
    { title: "Make coffee", completed: false }
])

autorun(() => {
    console.log(
        "Remaining:",
        todos
            .filter(todo => !todo.completed)
            .map(todo => todo.title)
            .join(", ")
    )
})
// 打印: 'Remaining: Make coffee'

todos[0].completed = false
// 打印: 'Remaining: Spoil tea, Make coffee'

todos[2] = { title: "Take a nap", completed: false }
// 打印: 'Remaining: Spoil tea, Make coffee, Take a nap'

todos.shift()
// 打印: 'Remaining: Make coffee, Take a nap'
```

使用autorun可以在响应式的数据发生改变的时候，自动执行对应的回调函数

可观察的数组还有一些特别好用的实用函数：

- `clear()` 从数组中清除所有元素。
- `replace(newItems)` 将数组中现有的元素全部替换成 newItems。
- `remove(value)` 根据 value 从数组中删除一个元素。如果找到并删除了元素，返回 `true`。

**注意：基本数据类型和类的实例不能转换成可观察的对象**

**`make(Auto)Observable` 和 `observable` 之间最主要的区别**在于，`make(Auto)Observable` 会修改你作为第一个参数传入的对象，而 `observable` 会创建一个可观察的 *副本* 对象

第二个区别是，`observable` 会创建一个 [`Proxy`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 对象，以便能够在你将该对象当作动态查询映射使用时捕获将要添加的属性。 如果你想把一个对象转化为可观察对象，而这个对象具有一个常规结构，其中所有的成员都是事先已知的，那么我们建议使用 `makeObservable`，因为非代理对象的速度稍快一些，而且它们在调试器和 `console.log` 中更容易检查。

因此，`make(Auto)Observable` 推荐在工厂函数中使用。 值得一提的是，可以将 `{ proxy: false }` 作为 option 传入 `observable` 获取非代理副本

## Actions

### 使用 actions 更新 state

用法：

- `action` *（注解）*
- `action(fn)`
- `action(name, fn)`

actions就是用来修改state中的数据的，

尽管 [`makeAutoObservable`](https://zh.mobx.js.org/observable-state.html#makeautoobservable) 可以自动帮你声明一部分 actions，但是 MobX 还是要求你声明你的 actions。Actions 可以帮助你更好的组织你的代码并提供以下性能优势：

1. 它们在 [transactions](https://zh.mobx.js.org/api.html#transaction) 内部运行。任何可观察对象在最外层的 action 完成之前都不会被更新，这一点保证了在 action 完成之前，action 执行期间生成的中间值或不完整的值对应用程序的其余部分都是不可见的。
2. 默认情况下，不允许在 actions 之外改变 state。这有助于在代码中清楚地对状态更新发生的位置进行定位。

`action` 注解应该仅用于会*修改* state 的函数。派生其他信息（执行查询或者过滤数据）的函数*不应该*被标记为 actions，以便 MobX 可以对它们的调用进行跟踪。 带有 `action` 注解的成员是不可枚举的。

makeObservable

```js
import { makeObservable, observable, action } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeObservable(this, {
            value: observable,
            increment: action
        })
    }

    increment() {
        // 观察者不会看到中间状态.
        this.value++
        this.value++
    }
}
```

makeAutoObservable

```js
import { makeAutoObservable } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeAutoObservable(this)
    }

    increment() {
        this.value++
        this.value++
    }
}
```

action.bound

```js
import { makeObservable, observable, action } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeObservable(this, {
            value: observable,
            increment: action.bound
        })
    }

    increment() {
        this.value++
        this.value++
    }
}

const doubler = new Doubler()

// 这样调用 increment 是安全的, 因为它已经被绑定了。
setInterval(doubler.increment, 1000)
```

Action(fn)

```js
import { observable, action } from "mobx"

const state = observable({ value: 0 })

const increment = action(state => {
    state.value++
    state.value++
})

increment(state)
```

runInAction

```js
import { observable, runInAction } from "mobx"

const state = observable({ value: 0 })

runInAction(() => {
    state.value++
    state.value++
})
```

### 使用 `action` 包装函数

为了尽可能地利用 MobX 的事务性，actions 应该尽可能被传到外围。如果一个类方法会修改 state，可以将其标记为 action。把事件处理函数标记为 actions 就更好了，因为最外层的事务起着决定性作用。一个未被标记的、会接着调用两个 actions 的事件处理函数仍然将会生成两个事务。

为了帮助创建基于 action 的事件处理函数，`action` 不仅仅是一个注解，更是一个高阶函数。可以使用函数将它作为一个参数来调用，在这种情况下它将会返回一个有着相同签名的使用 `action` 包装过的函数。

例如在 React 中，可以按照下面的方式包装 `onClick` 事件处理函数

```js
const ResetButton = ({ formState }) => (
    <button
        onClick={action(e => {
            formState.resetPendingUploads()
            formState.resetValues()
            e.stopPropagation()
        })}
    >
        Reset form
    </button>
)
```

为了更好的调试体验，我们推荐为被包装的函数命名，或者将名称作为 `action` 的第一个参数进行传递	

**注意：actions不会被跟踪**，就是不能触发autorun

actions 的另一个特征是它们是 [不可追踪](https://zh.mobx.js.org/api.html#untracked) 的。当从副作用或者计算值（非常罕见）中调用 action 时，该 action 读取的可观察对象将不会算作该 derivation 的依赖项。

`makeAutoObservable`，`extendObservable` 和 `observable` 使用一种特殊的 `action`， 叫做 `autoAction`， 它会在运行时确定函数是 derivation 还是 action。

### `action.bound`

用法：

- `action.bound` *（注解）*

`action.bound` 注解可用于将方法自动绑定到正确的实例，这样 `this` 会始终被正确绑定在函数内部

使用makeAutoObservable(o,{},{autoBind:true}) 自动绑定所有的actions和flow

 

```js
import { makeAutoObservable } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeAutoObservable(this, {}, { autoBind: true })
    }

    increment() {
        this.value++
        this.value++
    }
    
    *flow() {
        const response = yield fetch("http://example.com/value")
        this.value = yield response.json()
    }
}
```

### `runInAction`

用法：

- `runInAction(fn)`

使用这个工具函数来创建一个会被立即调用的临时 action。在异步进程中非常有用。 

### Actions 和继承

只有定义在**原型**上的函数可以被子类**覆盖**：

```js
class Parent {
    // on instance class filed
    arrowAction = () => {}

    // on prototype
    action() {}
    boundAction() {}

    constructor() {
        makeObservable(this, {
            arrowAction: action
            action: action,
            boundAction: action.bound,
        })
    }
}
class Child extends Parent {
    // THROWS: TypeError: Cannot redefine property: arrowAction
    arrowAction = () => {}

    // OK
    action() {}
    boundAction() {}

    constructor() {
        super()
        makeObservable(this, {
            arrowAction: override,
            action: override,
            boundAction: override,
        })
    }
}
```

想要将单个的 *action* **绑定** 到 `this`，可以使用 `action.bound` 代替箭头函数

### 异步 actions

从本质上讲，异步进程在 MobX 中不需要任何特殊处理，因为不论是何时引发的所有 reactions 都将会自动更新。 而且因为可观察对象是可变的，因此在 action 执行过程中保持对它们的引用一般是安全的。 然而，在异步进程中更新可观察对象的每个步骤（tick）都应该被标识为 `action`。 我们可以通过利用上述的 API 以多种方式实现这一点，如下所示。

例如，在处理 Promise 时，更新 state 的处理程序应该被 `action` 包装起来，或者被标记为 actions，如下所示。

```js
Promise 的决议处理程序是我们以内联的方式处理的，但是会在一开始的 action 执行完成之后运行，因此需要使用 action 对它们进行包装：

import { action, makeAutoObservable } from "mobx"

class Store {
    githubProjects = []
    state = "pending" // "pending", "done" or "error"

    constructor() {
        makeAutoObservable(this)
    }

    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(
            action("fetchSuccess", projects => {
                const filteredProjects = somePreprocessing(projects)
                this.githubProjects = filteredProjects
                this.state = "done"
            }),
            action("fetchError", error => {
                this.state = "error"
            })
        )
    }
}
```



如果 Promise 的处理函数是类的字段，它们将由 `makeAutoObservable` 自动包装为 `action`：

```javascript
import { makeAutoObservable } from "mobx"

class Store {
    githubProjects = []
    state = "pending" // "pending", "done" or "error"

    constructor() {
        makeAutoObservable(this)
    }

    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(this.projectsFetchSuccess, this.projectsFetchFailure)
    }

    projectsFetchSuccess = projects => {
        const filteredProjects = somePreprocessing(projects)
        this.githubProjects = filteredProjects
        this.state = "done"
    }

    projectsFetchFailure = error => {
        this.state = "error"
    }
}
```

`await` 之后的任何操作都不与其同在一个 tick 中，因此它们需要使用 action 包装。 在这里，我们可以利用 `runInAction`：

```javascript
import { runInAction, makeAutoObservable } from "mobx"

class Store {
    githubProjects = []
    state = "pending" // "pending", "done" or "error"

    constructor() {
        makeAutoObservable(this)
    }

    async fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        try {
            const projects = await fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            runInAction(() => {
                this.githubProjects = filteredProjects
                this.state = "done"
            })
        } catch (e) {
            runInAction(() => {
                this.state = "error"
            })
        }
    }
}
```

使用flow+generator

```js
import { flow, makeAutoObservable, flowResult } from "mobx"

class Store {
    githubProjects = []
    state = "pending"

    constructor() {
        makeAutoObservable(this, {
            fetchProjects: flow
        })
    }

    // 注意星号, 这是一个 generator 函数!
    *fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        try {
            // Yield 代替 await.
            const projects = yield fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            this.state = "done"
            this.githubProjects = filteredProjects
        } catch (error) {
            this.state = "error"
        }
    }
}

const store = new Store()
const projects = await flowResult(store.fetchProjects())
```

## Computers

使用：

- `computed` *（注解）*
- `computed(options)` *（注解）*
- `computed(fn, options?)`

计算值可以用来从其他可观察对象中派生信息。 计算值采用惰性求值，会缓存其输出，并且只有当其依赖的可观察对象被改变时才会重新计算。 它们在不被任何值观察时会被暂时停用。

计算值可以通过在 JavaScript [getters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) 上添加 `computed` 注解来创建。 使用 `makeObservable` 将 getter 声明为 computed。或者如果你希望所有的 getters 被自动声明为 `computed`，可以使用 `makeAutoObservable`，`observable` 或者 `extendObservable`

```js
import { makeObservable, observable, computed, autorun } from "mobx"

class OrderLine {
    price = 0
    amount = 1

    constructor(price) {
        makeObservable(this, {
            price: observable,
            amount: observable,
            total: computed
        })
        this.price = price
    }

    get total() {
        console.log("Computing...")
        return this.price * this.amount
    }
}

const order = new OrderLine(0)

const stop = autorun(() => {
    console.log("Total: " + order.total)
})
// Computing...
// Total: 0

console.log(order.total)
// (不会重新计算!)
// 0

order.amount = 5
// Computing...
// (无需 autorun)

order.price = 2
// Computing...
// Total: 10

stop()

order.price = 3
// 计算值和 autorun 都不会被重新计算.
```

相比之下，如果 `total` 没有被注解，那么 `autorun` 会把副作用运行 3 次， 因为它将直接依赖于 `total` 和 `amount`

![computed graph](https://zh.mobx.js.org/assets/computed-example.png)

上图是为以上示例创建的依赖图。

### 规则

使用计算值时，请遵循下面的最佳实践：

1. 它们不应该有副作用或者更新其他可观察对象。
2. 避免创建和返回新的可观察对象。
3. 它们不应该依赖非可观察对象的值

## 使用 reactions 处理副作用 {🚀}

为了理解 MobX，让我们看一下如何创建 reactions。 最简单的方式是使用 [`autorun`](https://zh.mobx.js.org/reactions.html#autorun) 工具函数。 除此之外，还有 [`reaction`](https://zh.mobx.js.org/reactions.html#reaction) 和 [`when`](https://zh.mobx.js.org/reactions.html#when)

### Autorun

用法：

- `autorun(effect: (reaction) => void)`

`autorun` 函数接受一个函数作为参数，每当该函数所观察的值发生变化时，它都应该运行。 当你自己创建 `autorun` 时，它也会运行一次。它仅仅对可观察状态的变化做出响应，比如那些你用 `observable` 或者 `computed` 注释的。

### tracking 如何工作

Autorun 通过在*响应式上下文*运行 `effect` 来工作。在给定的函数执行期间，MobX 会持续跟踪被 effect 直接或间接*读取*过的所有可观察对象和计算值。 一旦函数执行完毕，MobX 将收集并订阅所有被读取过的可观察对象，并等待其中任意一个再次发生改变。 一旦有改变发生，`autorun` 将会再次触发，重复整个过程。

![autorun](https://zh.mobx.js.org/assets/autorun.png)

这就是下面的示例的工作方式。

```js
import { makeAutoObservable, autorun } from "mobx"

class Animal {
    name
    energyLevel

    constructor(name) {
        this.name = name
        this.energyLevel = 100
        makeAutoObservable(this)
    }

    reduceEnergy() {
        this.energyLevel -= 10
    }

    get isHungry() {
        return this.energyLevel < 50
    }
}

const giraffe = new Animal("Gary")

autorun(() => {
    console.log("Energy level:", giraffe.energyLevel)
})

autorun(() => {
    if (giraffe.isHungry) {
        console.log("Now I'm hungry!")
    } else {
        console.log("I'm not hungry!")
    }
})

console.log("Now let's change state!")
for (let i = 0; i < 10; i++) {
    giraffe.reduceEnergy()
}
```

运行上面的代码，你将会看到下面的输出：

```
Energy level: 100
I'm not hungry!
Now let's change state!
Energy level: 90
Energy level: 80
Energy level: 70
Energy level: 60
Energy level: 50
Energy level: 40
Now I'm hungry!
Energy level: 30
Energy level: 20
Energy level: 10
Energy level: 0
```

autorun会比较前后值是否发生了改变，如果没有发生改变，就不会执行，当值发生变化的时候，才会重新执行

### Reaction

用法：

- `reaction(() => value, (value, previousValue, reaction) => { sideEffect }, options?)`.

第一个参数，是指需要监听那个state的变化，第二个参数是当数据变化的时候需要执行的函数，reaction

和autorun比较，autorun是针对所有的state，reaction是针对特定的state，而且reaction在初始化，不会执行，只有在state

发生变化时才会执行

在下面的例子中，reaction 只会在 `isHungry` 发生改变时被触发一次。 在 *effect* 函数中使用的 `giraffe.energyLevel` 的更改，并不会触发 *effect* 函数。如果你想要 `reaction` 也对这个值的变化做出反应， 你需要在 *data* 函数中访问并返回它。

```javascript
import { makeAutoObservable, reaction } from "mobx"

class Animal {
    name
    energyLevel

    constructor(name) {
        this.name = name
        this.energyLevel = 100
        makeAutoObservable(this)
    }

    reduceEnergy() {
        this.energyLevel -= 10
    }

    get isHungry() {
        return this.energyLevel < 50
    }
}

const giraffe = new Animal("Gary")

reaction(
    () => giraffe.isHungry,
    isHungry => {
        if (isHungry) {
            console.log("Now I'm hungry!")
        } else {
            console.log("I'm not hungry!")
        }
        console.log("Energy level:", giraffe.energyLevel)
    }
)

console.log("Now let's change state!")
for (let i = 0; i < 10; i++) {
    giraffe.reduceEnergy()
}
```

输出：

```
Now let's change state!
Now I'm hungry!
Energy level: 40
```

### When

使用：

- `when(predicate: () => boolean, effect?: () => void, options?)`
- `when(predicate: () => boolean, options?): Promise`

`when` 会观察并运行给定的 *predicate* 函数，直到其返回 `true`。 一旦 predicate 返回了 true，给定的 *effect* 函数就会执行并且自动执行器函数将会被清理掉。

如果你没有传入 `effect` 函数，`when` 函数返回一个 `Promise` 类型的 disposer，并允许你手动取消

`when` 对于以响应式的方式将值清理或取消掉十分有用。 例如：

```javascript
import { when, makeAutoObservable } from "mobx"

class MyResource {
    constructor() {
        makeAutoObservable(this, { dispose: false })
        when(
            // Once...
            () => !this.isVisible,
            // ... then.
            () => this.dispose()
        )
    }

    get isVisible() {
        // 表示此项目是否可见.
    }

    dispose() {
        // 清理一些资源.
    }
}
```

一旦 `isVisible` 变成 `false`，`dispose` 方法将会被调用， 并对 `MyResource` 做一些清理操作

### `await when(...)`

如果你没有提供 `effect` 函数，`when` 将会返回一个 `Promise`。这样会跟 `async / await` 很好地结合在一起，让你可以等待可观察对象中的变化。

```javascript
async function() {
    await when(() => that.isVisible)
    // etc...
}
```

如果要提前取消 `when`，可以对它返回的 Promise 调用 `.cancel()` 函数。

#### 规则

这里是一些 reactive context 需要遵守的规则：

1. 默认情况下，如果可观察对象发生了改变，受其影响的 reactions 会立即（同步）运行。然而，它们直到当前最外层的 (trans)action 执行结束后才会运行。
2. autorun 只会跟踪给定函数在同步执行过程中所读取的可观察对象，不会跟踪异步发生的变化。
3. autorun 不会跟踪被其调用的 action 所读取的可观察对象，因为 action 始终*不会被追踪*。

### Always dispose of reactions

传递给 `autorun`，`reaction` 和 `when` 的函数只有在它们观察的所有对象都被 GC 之后才会被 GC。原则上，它们一直等待可观察对象发生新的变化。 为了阻止 reactions 永远地等待下去，它们总是会返回一个 disposer 函数，该函数可以用来停止执行并且取消订阅所使用的任何可观察对象

```
const counter = observable({ count: 0 })

// 初始化一个 autorun 并且打印 0.
const disposer = autorun(() => {
    console.log(counter.count)
})

// 打印: 1
counter.count++

// 停止 autorun.
disposer()

// 不会打印消息.
counter.count++
```

我们强烈建议你，一旦不再需要这些方法中的副作用时，请务必调用它们所返回的 disposer 函数。 否则可能导致内存泄漏。

`reaction` 和 `autorun` 中 effect 函数的第二个参数 `reaction` 也可以被用来提前把 reaction 清理掉（通过调用 `reaction.dispose()`）。

```js
class Vat {
    value = 1.2

    constructor() {
        makeAutoObservable(this)
    }
}

const vat = new Vat()

class OrderLine {
    price = 10
    amount = 1
    constructor() {
        makeAutoObservable(this)

        // 这个 autorun 将会和本 OrderLine 实例一起进行 GC,
        // 因为它只使用了来自 `this` 的可观察对象.
        // 所以不一定非要在删除 OrderLine 实例后立刻把它清理掉。
        this.disposer1 = autorun(() => {
            doSomethingWith(this.price * this.amount)
        })

        // 这个 autorun 将不会和本 OrderLine 实例一起进行 GC,
        // 因为 vat 保存了对这个 autorun 的引用用于通知改变,
        // 这样反过来又会把 'this' 保存在作用域中。
        this.disposer2 = autorun(() => {
            doSomethingWith(this.price * this.amount * vat.value)
        })
    }

    dispose() {
        // 所以, 为了避免内存问题, 当不再需要 reactions 之后
        // 务必要调用 disposers.
        this.disposer1()
        this.disposer2()
    }
}
```

## 谨慎地使用 reactions！

就像上面已经说过的那样，你不会经常创建 reactions。 很有可能你的应用程序不会直接使用这些 API 中的任何一个，而只会通过比如使用 mobx-react 绑定中的 `observer` 这样间接的方式创建出 reaction。

在你创建 reaction 之前，最好检查一下它是否符合下面几条原则：

1. **只有在引起副作用的一方与副作用之间没有直接关系的情况下才使用 reaction**： 如果一个副作用会为了响应很小的一组 events 或 actions 而执行，那么直接从那些特定的 action 中触发这个副作用通常会更容易理解。例如，如果按下表单提交按钮会导致一个 POST 网络请求的发送，那么为了响应 `onclick` 事件，直接触发这个副作用就会比通过 reaction 间接触发更容易理解。相比之下，如果你对表单状态的一切修改最后都会被自动存储到 localStorage，那么使用一个 reaction 可能就会很有帮助，这样你就不用在每个独立的 `onChange` 事件中触发这个副作用了。
2. **reactions 不应该更新其他可观察对象**：这个 reaction 是否会修改其他可观察对象？如果答案是肯定的，那么你一般应该把你想要更新的可观察对象注解为 [`computed`](https://zh.mobx.js.org/computeds.html) 值。例如，如果一个待办事项的集合 `todos` 发生了变化，那么请不要使用 reaction 来计算剩余待办 `remainingTodos` 的数量，而要把 `remainingTodos` 注解为计算值。这将使得代码更容易理解和调试。reaction 不应该计算生成新的数据，而只应该触发副作用。
3. **reactions 应该是独立的**：你的代码是否依赖其他必须首先运行的 reaction？如果发生这种情况，你可能违反了第一条规则， 你可以选择将你需要创建的新 reaction 合并到它所依赖 reaction 中。MobX 并不能保证 reaction 的执行顺序。

有些实践并不符合上述原则。这就是为什么它们是*原则*，而不是*法则*。 但是，例外情况很少见，只有在万不得已的情况下才违反它们。
