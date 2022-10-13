---
title: Redux笔记
date: 2022-09-08 13:57:45
tags:
categories:
- [react, note]
---

#### ##纯函数

 确定的输入，一定会产生确定的输出；

函数在执行过程中，不能产生副作用

#### Store

一个store文件夹由4个文件组成

actionCreator.js

这个文件是用来编写action,action是一个普通的JavaScript对象，用来描述这次更新的type和content

强制使用action的好处是可以清晰的知道数据到底发生了什么样的变化，所有的数据变化都是可跟追、可预测的；

当然，目前我们的action是固定的对象

真实应用中，我们会通过函数来定义，返回一个action



Redux要求我们通过action来更新数据,通过dispatch action来更新数据

```
//actionCreator.js
const { ADD_NUMBER, CHANGE_NAME } = require("./constants")
const changeNameAction = (name) => ({
  type: CHANGE_NAME,
  name
})
const addNumberAction = (num) => ({
  type: ADD_NUMBER,
  num
})
module.exports = {
  changeNameAction,
  addNumberAction
}

```

constants.js 用来定义type常量

```js
const ADD_NUMBER = "add_number"
const CHANGE_NAME = "change_name"

module.exports = {
  ADD_NUMBER,
  CHANGE_NAME
}

```

reducer.js

reducer是将state和action联系起来的桥梁，reducer是纯函数，reducer做的事情是将传入的state和action结合起来生成新的state

```js
const { ADD_NUMBER, CHANGE_NAME } = require("./constants")

// 初始化的数据
const initialState = {
  name: "why",
  counter: 100
}

function reducer(state = initialState, action) {
  switch(action.type) {
    case CHANGE_NAME:
      return { ...state, name: action.name }
    case ADD_NUMBER:
      return { ...state, counter: state.counter + action.num }
    default:
      return state
  }
}

module.exports = reducer
```

index.js

创建store实例

```js
const { createStore } = require("redux")
const reducer =  require("./reducer.js")

// 创建的store
const store = createStore(reducer)

module.exports = store

```

使用store中的数据

```js
const store = require("./store")

console.log(store.getState())

```

修改store中的数据

```js
store.dispatch(changeNameAction("kobe"))
```

订阅store中的数据

```js
const store = require("./store")

const unsubscribe = store.subscribe(() => {
  console.log("订阅数据的变化:", store.getState())
})

// 修改store中的数据: 必须action
store.dispatch({ type: "change_name", name: "kobe" })
store.dispatch({ type: "change_name", name: "lilei" })

unsubscribe()
```

#### redux 三大核心 store action reducer

#### redux 的三大原则

- 单一数据源
  1.  整个应用程序的state被存储在一颗object tree中，并且这个object tree只存储在一个 store 中
  2. Redux并没有强制让我们不能创建多个Store，但是那样做并不利于数据的维护
  3. 单一的数据源可以让整个应用程序的state变得方便维护、追踪、修改
- State是只读的
  1. ​	唯一修改State的方法一定是触发action，不要试图在其他地方通过任何的方式来修改State
  2. ​    这样就确保了View或网络请求都不能直接修改state，它们只能通过action来描述自己想要如何修改state
  3. 这样可以保证所有的修改都被集中化处理，并且按照严格的顺序来执行，所以不需要担心race condition（竟态）的问题
- 使用纯函数来执行修改
  1. ​	通过reducer将 旧state和 actions联系在一起，并且返回一个新的State
  2. ​    随着应用程序的复杂度增加，我们可以将reducer拆分成多个小的reducers，分别操作不同state tree的一部分
  3.    但是所有的reducer都应该是纯函数，不能产生任何的副作用

<img src="https://img1.imgtp.com/2022/09/13/UQYkeCuR.png" alt="QQ截图20220913015123.png" style="zoom:200%;" />

![QQ截图20220913015145.png](https://img1.imgtp.com/2022/09/13/6ShJ2AyZ.png)

#### react和redux结合使用

react-redux的使用

```html
//index.js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import { Provider } from "react-redux"
import store from "./store"

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  // <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  // </React.StrictMode>
);
```

```
//在组件中使用
import React, { PureComponent } from 'react'
import { connect } from "react-redux"
// import store from "../store"
import { addNumberAction, subNumberAction } from '../store/counter'

export class About extends PureComponent {

  calcNumber(num, isAdd) {
    if (isAdd) {
      this.props.addNumber(num)
    } else {
      this.props.subNumber(num)
    }
  }

  render() {
    const { counter, banners, recommends, userInfo } = this.props

    return (
      <div>
        <div className='user'>
          <h2>nickname: {userInfo.nickname}</h2>
        </div>
        <h2>About Page: {counter}</h2>
        <div>
          <button onClick={e => this.calcNumber(6, true)}>+6</button>
          <button onClick={e => this.calcNumber(88, true)}>+88</button>
          <button onClick={e => this.calcNumber(6, false)}>-6</button>
          <button onClick={e => this.calcNumber(88, false)}>-88</button>
        </div>
        <div className='banner'>
          <h2>轮播图数据:</h2>
          <ul>
            {
              banners.map((item, index) => {
                return <li key={index}>{item.title}</li>
              })
            }
          </ul>
        </div>
        <div className='recommend'>
          <h2>推荐数据:</h2>
          <ul>
            {
              recommends.map((item, index) => {
                return <li key={index}>{item.title}</li>
              })
            }
          </ul>
        </div>
      </div>
    )
  }
}
const mapStateToProps = (state) => ({
  counter: state.counter.counter,
  banners: state.home.banners,
  recommends: state.home.recommends,
  userInfo: state.user.userInfo
})

const mapDispatchToProps = (dispatch) => ({
  addNumber(num) {
    dispatch(addNumberAction(num))
  },
  subNumber(num) {
    dispatch(subNumberAction(num))
  }
})

export default connect(mapStateToProps, mapDispatchToProps)(About)
```

没有使用react-redux的写法

```js
import React, { PureComponent } from 'react'
import store from "../store"
import { addNumberAction } from '../store/counter'

export class Home extends PureComponent {
  constructor() {
    super()

    this.state = {
      counter: store.getState().counter.counter,

      message: "Hello World",
      friends: [
        {id: 111, name: "why"},
        {id: 112, name: "kobe"},
        {id: 113, name: "james"},
      ]
    }
  }

  componentDidMount() {
    store.subscribe(() => {
      const state = store.getState().counter
      this.setState({ counter: state.counter })
    })
  }

  addNumber(num) {
    store.dispatch(addNumberAction(num))
  }

  render() {
    const { counter } = this.state

    return (
      <div>
        <h2>Home Counter: {counter}</h2>
        <div>
          <button onClick={e => this.addNumber(1)}>+1</button>
          <button onClick={e => this.addNumber(5)}>+5</button>
          <button onClick={e => this.addNumber(8)}>+8</button>
        </div>
      </div>
    )
  }
}

export default Home
```

#### 我们可以将react组件中的异步操作放到redux中，使用中间件，中间件的目的是在dispatch的action和最终达到的reducer之间，扩展一些自己的代码

使用redux-thunk来发布异步请求

```js
import { createStore, applyMiddleware, compose } from "redux"
import thunk from "redux-thunk"
import reducer from "./reducer"

// 正常情况下 store.dispatch(object)
// 想要派发函数 store.dispatch(function)

// redux-devtools
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({trace: true}) || compose;
const store = createStore(reducer, composeEnhancers(applyMiddleware(thunk)))

export default store

```

定义一个返回函数的action

```js
export const fetchHomeMultidataAction = () => {
  // 如果是一个普通的action, 那么我们这里需要返回action对象
  // 问题: 对象中是不能直接拿到从服务器请求的异步数据的
  // return {}

  return function(dispatch, getState) {
    // 异步操作: 网络请求
    // console.log("foo function execution-----", getState().counter)
    axios.get("http://123.207.32.32:8000/home/multidata").then(res => {
      const banners = res.data.data.banner.list
      const recommends = res.data.data.recommend.list

      // dispatch({ type: actionTypes.CHANGE_BANNERS, banners })
      // dispatch({ type: actionTypes.CHANGE_RECOMMENDS, recommends })
      dispatch(changeBannersAction(banners))
      dispatch(changeRecommendsAction(recommends))
    })
  }

  // 如果返回的是一个函数, 那么redux是不支持的
  // return foo
}
```

#### 定义applyMiddleware中间件

```js
function applyMiddleware(store, ...fns) {
  fns.forEach(fn => {
    fn(store)
  })
}

export default applyMiddleware

```

#### 定义log中间件

```js
function log(store) {
  const next = store.dispatch

  function logAndDispatch(action) {
    console.log("当前派发的action:", action)
    // 真正派发的代码: 使用之前的dispatch进行派发
    next(action)
    console.log("派发之后的结果:", store.getState())
  }

  // monkey patch: 猴补丁 => 篡改现有的代码, 对整体的执行逻辑进行修改
  store.dispatch = logAndDispatch
}

export default log
```

#### 定义thunk中间件

```js
function thunk(store) {
  const next = store.dispatch
  function dispatchThunk(action) {
    if (typeof action === "function") {
      action(store.dispatch, store.getState)
    } else {
      next(action)
    }
  }
  store.dispatch = dispatchThunk
}

export default thunk

```

```js
import { createStore, compose, combineReducers } from "redux"
import { log, thunk, applyMiddleware } from "./middleware"
// import thunk from "redux-thunk"

import counterReducer from "./counter"
import homeReducer from "./home"
import userReducer from "./user"

// 正常情况下 store.dispatch(object)
// 想要派发函数 store.dispatch(function)

// 将两个reducer合并在一起
const reducer = combineReducers({
  counter: counterReducer,
  home: homeReducer,
  user: userReducer
})

// combineReducers实现原理(了解)
// function reducer(state = {}, action) {
//   // 返回一个对象, store的state
//   return {
//     counter: counterReducer(state.counter, action),
//     home: homeReducer(state.home, action),
//     user: userReducer(state.user, action)
//   }
// }

// redux-devtools
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({trace: true}) || compose;
const store = createStore(reducer)

applyMiddleware(store, log, thunk)

export default store
```

#### combineReducers

redux给我们提供了一个combineReducers函数可以方便的让我们对多个reducer进行合并

事实上，它也是将我们传入的reducers合并到一个对象中，最终返回一个combination的函数（相当于我们之前的reducer函 数了）； 

 在执行combination函数的过程中，它会通过判断前后返回的数据是否相同来决定返回之前的state还是新的state；

 新的state会触发订阅者发生对应的刷新，而旧的state可以有效的组织订阅者发生刷新



#### Redux Toolkit的用法

```
npm install @reduxjs/toolkit react-redux
```

Redux Toolkit 是官方推荐的编写 Redux 逻辑的方法。

redux toolkit API 

- configureStore:包装createStore以提供简化的配置选项和良好的默认值。它可以自动组合你的 slice reducer，添加你提供 的任何 Redux 中间件，redux-thunk默认包含，并启用 Redux DevTools Extension
- createSlice:接受reducer函数的对象、切片名称和初始状态值，并自动生成切片reducer，并带有相应的actions
- createAsyncThunk:接受一个动作类型字符串和一个返回promise的函数，并生成一个pending/fulfilled/rejected基于该promise分派动作类型的 thunk

```js
//store.index.js
import { configureStore } from "@reduxjs/toolkit"

import counterReducer from "./features/counter"
import homeReducer from "./features/home"

const store = configureStore({
  reducer: {
    counter: counterReducer,
    home: homeReducer
  }
})

export default store

```

```js
///features/counter.js
import { createSlice } from "@reduxjs/toolkit"

const counterSlice = createSlice({
  name: "counter",//用户标记slice的名词,在之后的redux-devtool中会显示对应的名词；
  //初始化值
  initialState: {
    counter: 888
  },
  //相当于之前的reducer函数
  reducers: {
    addNumber(state, { payload }) {
      state.counter = state.counter + payload
    },
    subNumber(state, { payload }) {
      state.counter = state.counter - payload
    }
  }
})

export const { addNumber, subNumber } = counterSlice.actions
export default counterSlice.reducer

```

//在组件中使用

```js
import React, { PureComponent } from 'react'
import { connect } from "../hoc"
import { addNumber } from "../store/features/counter"
export class About extends PureComponent {
  render() {
    const { counter } = this.props

    return (
      <div>
        <h2>About Counter: {counter}</h2>
      </div>
    )
  }
}
const mapStateToProps = (state) => ({
  counter: state.counter.counter
})
const mapDispatchToProps = (dispatch) => ({
  addNumber(num) {
    dispatch(addNumber(num))
  }
})
export default connect(mapStateToProps, mapDispatchToProps)(About)
```

#### Redux Toolkit的异步操作

Redux Toolkit默认已经给我们继承了Thunk相关的功能：createAsyncThunk

```js
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit'
import axios from 'axios'

export const fetchHomeMultidataAction = createAsyncThunk(
  "fetch/homemultidata", 
  async (extraInfo, { dispatch, getState }) => {
    // console.log(extraInfo, dispatch, getState)
    // 1.发送网络请求, 获取数据
    const res = await axios.get("http://123.207.32.32:8000/home/multidata")

    // 2.取出数据, 并且在此处直接dispatch操作(可以不做)
    const banners = res.data.data.banner.list
    const recommends = res.data.data.recommend.list
    dispatch(changeBanners(banners))
    dispatch(changeRecommends(recommends))

    // 3.返回结果, 那么action状态会变成fulfilled状态
    return res.data
})

const homeSlice = createSlice({
  name: "home",
  initialState: {
    banners: [],
    recommends: []
  },
  reducers: {
    changeBanners(state, { payload }) {
      state.banners = payload
    },
    changeRecommends(state, { payload }) {
      state.recommends = payload
    }
  },
  // extraReducers: {
  //   [fetchHomeMultidataAction.pending](state, action) {
  //     console.log("fetchHomeMultidataAction pending")
  //   },
  //   [fetchHomeMultidataAction.fulfilled](state, { payload }) {
  //     state.banners = payload.data.banner.list
  //     state.recommends = payload.data.recommend.list
  //   },
  //   [fetchHomeMultidataAction.rejected](state, action) {
  //     console.log("fetchHomeMultidataAction rejected")
  //   }
  // }
  extraReducers: (builder) => {
    // builder.addCase(fetchHomeMultidataAction.pending, (state, action) => {
    //   console.log("fetchHomeMultidataAction pending")
    // }).addCase(fetchHomeMultidataAction.fulfilled, (state, { payload }) => {
    //   state.banners = payload.data.banner.list
    //   state.recommends = payload.data.recommend.list
    // })
  }
})

export const { changeBanners, changeRecommends } = homeSlice.actions
export default homeSlice.reducer
```

#### Redux Toolkit的数据不可变性

我们经常会进行浅拷贝来完成某些操作，但是浅拷贝事实上也是存在问题的

比如过大的对象，进行浅拷贝也会造成性能的浪费

比如浅拷贝后的对象，在深层改变时，依然会对之前的对象产生影响

事实上Redux Toolkit底层使用了immerjs的一个库来保证数据的不可变性

为了节约内存，又出现了一个新的算法：Persistent Data Structure（持久化数据结构或一致性 数据结构）；

​	用一种数据结构来保存数据；

​	当数据被修改时，会返回一个对象，但是新的对象会尽可能的利用之前的数据结构而不会 对内存造成浪费；





#### 自定义connect函数

```js
//connect.js
// connect的参数:
// 参数一: 函数
// 参数二: 函数
// 返回值: 函数 => 高阶组件

import { PureComponent } from "react";
import { StoreContext } from "./StoreContext";
// import store from "../store"

export function connect(mapStateToProps, mapDispatchToProps, store) {
  // 高阶组件: 函数
  return function(WrapperComponent) {
    class NewComponent extends PureComponent {
      constructor(props, context) {
        super(props)
        
        this.state = mapStateToProps(context.getState())
      }

      componentDidMount() {
        this.unsubscribe = this.context.subscribe(() => {
          // this.forceUpdate()
          this.setState(mapStateToProps(this.context.getState()))
        })
      }

      componentWillUnmount() {
        this.unsubscribe()
      }

      render() {
        const stateObj = mapStateToProps(this.context.getState())
        const dispatchObj = mapDispatchToProps(this.context.dispatch)
        return <WrapperComponent {...this.props} {...stateObj} {...dispatchObj}/>
      }
    }

    NewComponent.contextType = StoreContext

    return NewComponent
  }
}

```

```js
//StoreContext.js
import { createContext } from "react";

export const StoreContext = createContext()

```

