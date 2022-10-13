---
title: mobx的使用二
date: 2022-10-08 22:40:53
tags:
categories:
- [react,mobx]
---

# 集成React（react-integration）

用法:

```javascript
import { observer } from "mobx-react-lite" // Or "mobx-react".

const MyComponent = observer(props => ReactElement)
```

MobX 可以独立于 React 运行, 但是他们通常是结合在一起使用, 在 [Mobx的宗旨（The gist of MobX）](https://zh.mobx.js.org/the-gist-of-mobx.html) 一文中你会经常看见集成React最重要的一部分：用于包裹React Component的 `observer` [HOC](https://reactjs.org/docs/higher-order-components.html)方法。

`observer` 是你可以自主选择的，[在安装时（during installation）](https://zh.mobx.js.org/installation.html#installation)独立提供的 React bindings 包。 在下面的例子中,我们将使用更加轻量的[`mobx-react-lite` 包](https://github.com/mobxjs/mobx/tree/main/packages/mobx-react-lite)。

```js
import React from "react"
import ReactDOM from "react-dom"
import { makeAutoObservable } from "mobx"
import { observer } from "mobx-react-lite"

class Timer {
    secondsPassed = 0

    constructor() {
        makeAutoObservable(this)
    }

    increaseTimer() {
        this.secondsPassed += 1
    }
}

const myTimer = new Timer()

//被`observer`包裹的函数式组件会被监听在它每一次调用前发生的任何变化
const TimerView = observer(({ timer }) => <span>Seconds passed: {timer.secondsPassed}</span>)

ReactDOM.render(<TimerView timer={myTimer} />, document.body)

setInterval(() => {
    myTimer.increaseTimer()
}, 1000)
```

`observer` HOC 将自动订阅 React components 中任何 *在渲染期间* 被使用的 *可被观察的对象* 。 因此, 当任何可被观察的对象 *变化* 发生时候 组件会自动进行重新渲染（re-render）。 它还会确保组件在 *没有变化* 发生的时候不会进行重新渲染（re-render）。 但是, 更改组件的可观察对象的不可读属性, 也不会触发重新渲染（re-render）。

在实际项目中，这一特性使得MobX应用程序能够很好的进行开箱即用的优化，并且通常不需要任何额外的代码来防止过度渲染。

要想让`observer`生效, 并不需要关心这些对象 *如何传递到* 组件的（它们只要能传递给组件即可 ·译者注）, 只需要关心他们是否是可读的。 深层嵌套的可观察对象也没有问题, 复杂的表达式类似 `todos[0].author.displayName` 也是可以使用的。 与其他必须显式声明或预先计算数据依赖关系的框架（例如 selectors）相比，这种发生的订阅机制就显得更加精确和高效。

### `observer` 组件中使用外部状态 （Using external state in `observer` components）

使用props的方式

可被观察对象可以通过组件的props属性传入 (在下面的例子中):

```javascript
import { observer } from "mobx-react-lite"

const myTimer = new Timer() // See the Timer definition above.

const TimerView = observer(({ timer }) => <span>Seconds passed: {timer.secondsPassed}</span>)

// 通过props传递myTimer.
ReactDOM.render(<TimerView timer={myTimer} />, document.body)
```

使用全局变量的方式

虽然我们不关心是 *如何* 引用（reference）的可观察对象,但是我们可以使用 （consume） 外部作用域（outer scopes directly）的可观察对象 (类似通过 import这样的方法, 等等)：

```javascript
const myTimer = new Timer() //  Timer 定义在上面.

// 没有props, `myTimer` 立刻变成了闭包。
const TimerView = observer(() => <span>Seconds passed: {myTimer.secondsPassed}</span>)

ReactDOM.render(<TimerView />, document.body)
```

直接使用可观察对象效果很好，但是这通常会是通过模块引入，这种写法可能会使单元测试变得复杂。 因此，我们建议使用React Context。

使用[React Context](https://reactjs.org/docs/context.html)共享整个可观察子树是一种很不错的选择：

```javascript
import {observer} from 'mobx-react-lite'
import {createContext, useContext} from "react"

const TimerContext = createContext<Timer>()

const TimerView = observer(() => {
    // 从context中获取timer.
    const timer = useContext(TimerContext) // 可以在上面查看 Timer的定义。
    return (
        <span>Seconds passed: {timer.secondsPassed}</span>
    )
})

ReactDOM.render(
    <TimerContext.Provider value={new Timer()}>
        <TimerView />
    </TimerContext.Provider>,
    document.body
)
```

需要注意的是我们并不推荐每一个不同的 `值（value）` 都通过不同的 `Provider`来传递 . 在使用Mobx的过程中不需要这样做, 因为共享的可观察对象会更新他自己。

### 在`observer` 组件中使用全局可观察对象（Using local observable state in `observer` components）

因为使用 `observer` 的可观察对象可以来自任何地方, 他们也可以使用local state（全局的state·译者注）。 再次声明，不同操作方式对于我们而言都是有价值的。

使用全局可观察对象的最简单的方式就是通过`useState`去存储一个全局可观察对象的引用。 需要注意的是, 因为我们不需要替换全局可观察对象的引用,所以我们其实可以完全不声明`useState`的更新方法:

```javascript
import { observer } from "mobx-react-lite"
import { useState } from "react"

const TimerView = observer(() => {
    const [timer] = useState(() => new Timer()) // Timer的定义在上面（正如上面所说的那样这里我们忽略了更新方法的定义·译者注）。
    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

如果你想要类似我们官方的例子那样自动更新 timer , 使用`useEffect` 可能是 React 中比较典型的写法：

```javascript
useEffect(() => {
    const handle = setInterval(() => {
        timer.increaseTimer()
    }, 1000)
    return () => {
        clearInterval(handle)
    }
}, [timer])
```

如刚才说的那样, 直接创建一个可观察的对象，而不是使用classes（这里的类指的是全局定义的Mobx state，在它们是使用class声明的）。 我们可以参考 [observable](https://zh.mobx.js.org/observable-state.html#observable)这篇文章：

```javascript
import { observer } from "mobx-react-lite"
import { observable } from "mobx"
import { useState } from "react"

const TimerView = observer(() => {
    const [timer] = useState(() =>
        observable({
            secondsPassed: 0,
            increaseTimer() {
                this.secondsPassed++
            }
        })
    )
    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

`const [store] = useState(() => observable({ /* something */}))` 是非常通用的一套写法， 为了简化这个写法我们可以调用`mobx-react-lite` 包中的 [`useLocalObservable`](https://github.com/mobxjs/mobx-react#uselocalobservable-hook) hook ,可以将上面的例子简化成：

```javascript
import { observer, useLocalObservable } from "mobx-react-lite"
import { useState } from "react"

const TimerView = observer(() => {
    const timer = useLocalObservable(() => ({
        secondsPassed: 0,
        increaseTimer() {
            this.secondsPassed++
        }
    }))
    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

### 不要将可观察对象传递到 不是`observer`的组件中（Don't pass observables into components that aren't `observer`）

通过`observer`包裹的组件 *只可以* 订阅到在 *他们自己* 渲染的期间的可观察对象. 如果要将可观察对象 objects / arrays / maps 传递到子组件中, 他们必须被 `observer` 包裹。 通过callback回调的组件也是一样。

如果你非要传递可观察对象到未被`observer`包裹的组件中， 要么是因为它是第三方组件，要么你需要组件对Mobx无感知，那你必须在传递前 [转换可观察对象为显式 （convert the observables to plain JavaScript values or structures）](https://zh.mobx.js.org/observable-state.html#将-observable-转换回普通的-javascript-集合) 。

关于上述的详细描述, 可以看一下下面的使用 `todo` 对象的例子， 一个 `TodoView` (observer)组件和一个虚构的接收一组对象映射入参的不是`observer`的`GridRow`组件

```js
class Todo {
    title = "test"
    done = true

    constructor() {
        makeAutoObservable(this)
    }
}

const TodoView = observer(({ todo }: { todo: Todo }) =>
   // 错误: GridRow 不能获取到 todo.title/ todo.done 的变更
   //       因为他不是一个观察者（observer。
   return <GridRow data={todo} />

   // 正确:在 `TodoView` 中显式的声明相关的`todo` ，
   //      到data中。
   return <GridRow data={{
       title: todo.title,
       done: todo.done
   }} />

   // 正确: 使用 `toJS`也是可以的, 并且是更清晰直白的方式。
   return <GridRow data={toJS(todo)} />
)
```

### 回调组件可能会需要`<Observer>`（ Callback components might require `<Observer>`）

想象一下在同样的例子中, `GridRow` 携带一个 `onRender`回调函数。 `onRender` 是 `GridRow`渲染生命周期的一部分, 而不是 `TodoView` 的render (甚至在语法层面都能看出来)，我们不得不保证回调组件是一个 `observer` 组件。 或者，我们可以使用 [``](https://github.com/mobxjs/mobx-react#observer)创建一个匿名观察者：

```js
const TodoView = observer(({ todo }: { todo: Todo }) => {
    // 错误: GridRow.onRender 不能获得 todo.title / todo.done 中的改变
    //        因为它不是一个观察者（observer） 。
    return <GridRow onRender={() => <td>{todo.title}</td>} />

    // 正确: 将回调组件通过Observer包裹将会正确的获得变化。
    return <GridRow onRender={() => <Observer>{() => <td>{todo.title}</td>}</Observer>} />
})
```

**mobx-react vs. mobx-react-lite**

在本文中我们使用 `mobx-react-lite` 作为默认包。 [mobx-react](https://github.com/mobxjs/mobx-react/) 是他的大兄弟，它里面也引用了 `mobx-react-lite` 包。 它提供了很多在新项目中不再需要的特性， mobx-react附加的特性有：

1. 对于React class components的支持。
2. `Provider` 和`inject`. MobX的这些东西在有 React.createContext 替代后变得不必要了。
3. 特殊的观察对象 `propTypes`。

要注意 `mobx-react` 是全量包，也会暴露 `mobx-react-lite`包中的任何方法,其中包含对函数组件的支持。 如果你使用 `mobx-react`，那就不要添加 `mobx-react-lite` 的依赖和引用了。

**observer 🆚 React.memo**

`observer` 会自动的使用 `memo`, 所以 `observer` 不需要再包裹 `memo`。 `memo` 会被 observer 组件安全的使用，因为任何在props中的改变(很深的) 都会被`observer`响应。

**应用`observer` 到基于class的组件**

如上所述，class 组件只在`mobx-react`包中得到支持， `mobx-react-lite`并不支持。 简而言之，你可以和函数式组件一样使用 `observer`包裹class 组件：

```javascript
import React from "React"

const TimerView = observer(
    class TimerView extends React.Component {
        render() {
            const { timer } = this.props
            return <span>Seconds passed: {timer.secondsPassed} </span>
        }
    }
)
```

可以阅读 [mobx-react 文档](https://github.com/mobxjs/mobx-react#api-documentation) 获得更详细的信息。

**当你想要将`observer` 和其他高阶组件（HOC·译者注）一起使用, 需要首先调用 `observer`**

当 `observer` 需要和装饰器或者其他高阶组件（HOC）一起使用时，请确保 `observer` 是最内层的 (最先调用的) 装饰器，否则的话它可能不会工作

**从 props导出计算属性**

在某些情况下你的组件本地可观察对象（local observables）的计算属性可能依赖于一些你组件接受到的参数（props）。 但是,这一系列从React组件接收到的参数（props）本身并不是可观察对象，所以更改这些组件的属性（props）并不会使得计算属性响应。你可能需要手动的从最新的数据来更新全局可观察对象的状态来触发计算属性更新。



```javascript
import { observer, useLocalObservable } from "mobx-react-lite"
import { useEffect } from "react"

const TimerView = observer(({ offset }) => {
    const timer = useLocalObservable(() => ({
        offset, // 初始化offset
        secondsPassed: 0,
        increaseTimer() {
            this.secondsPassed++
        },
        get offsetTime() {
            return this.secondsPassed - this.offset // 这里的'offset'不是'props'传入的那个
        }
    }))

    useEffect(() => {
        //同步来自 'props' 的偏差到可观察对象 'timer'
        timer.offset = offset
    }, [offset])

    //作为demo用途，初始化一个定时器.
    useEffect(() => {
        const handle = setInterval(timer.increaseTimer, 1000)
        return () => {
            clearInterval(handle)
        }
    }, [])

    return <span>Seconds passed: {timer.offsetTime}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

在实际项目中你可能很少需要这种写法, 因为 `return <span>Seconds passed: {timer.secondsPassed - offset}</span>` 更加简单, 虽然是稍微低效率的解决方案。

**useEffect 与 可观察对象**

`useEffect` 可以被用于触发需要发生的副作用, 它将会被约束在React 组建的生命周期中。 使用 `useEffect`需要指定详细的依赖。 对于 MobX 却不是必须的, 因为 MobX 拥有一种真正的能检查到依赖发发生的方法， `autorun`。 结合 `autorun`可以很轻松的在生命周期组件中使用`useEffect`：

```javascript
import { observer, useLocalObservable, useAsObservableSource } from "mobx-react-lite"
import { useState } from "react"

const TimerView = observer(() => {
    const timer = useLocalObservable(() => ({
        secondsPassed: 0,
        increaseTimer() {
            this.secondsPassed++
        }
    }))

    // 在Effect方法之上触发可观察对象变化。
    useEffect(
        () =>
            autorun(() => {
                if (timer.secondsPassed > 60) alert("Still there. It's a minute already?!!")
            }),
        []
    )

    // 作为demo用途在Effect里定义一个定时器。
    useEffect(() => {
        const handle = setInterval(timer.increaseTimer, 1000)
        return () => {
            clearInterval(handle)
        }
    }, [])

    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

需要注意的是我们在effect方法返回了一个创建自`autorun` 的清除方法。 这一点是非常重要的, 因为他确保了 `autorun` 在组件卸载的时候被清除了！

依赖数组可以保持为空，除非是一个不可观察对象的值需要触发autorun重新运行，你才需要将它添加到这里面。 请确保你的格式正确,你可以创建一个`定时器（timer）` (上面的例子中) 作为依赖。 这是安全并且无副作用的， 因为它引用的依赖根本不会改变。

如果你不想显式的在Effect中定义可观察对象请使用`reaction`而不是`autorun`，他们的传参是完全相同的。

# 优化React组件渲染 {🚀}
