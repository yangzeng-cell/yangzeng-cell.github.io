---
title: react18 学习笔记
date: 2022-08-30 15:44:55
categories:
- [react, note]
---


#### React开发依赖

react开发必须依赖三个库：

- react   包含react所有必须的核心代码

- react-dom react渲染在不同平台所需要的核心代码

- babel  将jsx转换成React代码的工具

  react 包含react web和react-native所拥有的核心代码

  react-dom针对web和native完成的事情不同

  ​	web端react-dom会将jsx最终渲染成真实的DOM，显示在浏览器

  ​	native端 react-dom会将jsx最终渲染成原生的控件

  babel是将jsx代码转成react.createElement

#### React组件化开发

​	react中数据依赖来自两方面

​		参与界面更新的数据   当数据变量时，需要更新组件渲染的内容

​		不参与界面更新的数据  当数据变量时，不需要更新将组建渲染的内容，参与界面更新的数据我们也可以称之为是参与数据流，这个数据是定义在当前对象的state中，我们可以通过在构造函数中 this.state = {定义的数据}

 当我们的数据发生变化时，我们可以调用 this.setState 来更新数据，并且通知React进行update操作；在进行update操作时，就会重新调用render函数，并且使用最新的数据，来渲染界面

- 根据组件的定义方式，可以分为：函数组件(Functional Component )和类组件(Class Component)；

- 根据组件内部是否有状态需要维护，可以分成：无状态组件(Stateless Component )和有状态组件(Stateful Component)；

- 根据组件的不同职责，可以分成：展示型组件(Presentational Component)和容器型组件(Container Component)；
- 函数组件、无状态组件、展示型组件主要关注UI的展示；
- 类组件、有状态组件、容器型组件主要关注数据逻辑

#### JSX的条件渲染

​	在react中，所有条件判断都和普通的js代码一致，而vue中会使用v-if/v-show等来控制，react中没有这样封装好的指令

​	react常见的条件渲染方式

- 条件判断语句

- 三元运算符

- 与运算符&&

- display来控制

  ```js
  <script type="text/babel">
      // 1.定义App根组件
      class App extends React.Component {
        constructor() {
          super()
          this.state = {
            message: "Hello World",
  
            isReady: false,
  
            friend: undefined
          }
        }
  
        render() {
          const { isReady, friend } = this.state
  
          // 1.条件判断方式一: 使用if进行条件判断
          let showElement = null
          if (isReady) {
            showElement = <h2>准备开始比赛吧</h2>
          } else {
            showElement = <h1>请提前做好准备!</h1>
          }
  
          return (
            <div>
              {/* 1.方式一: 根据条件给变量赋值不同的内容 */}
              <div>{showElement}</div>
  
              {/* 2.方式二: 三元运算符 */}
              <div>{ isReady ? <button>开始战斗!</button>: <h3>赶紧准备</h3> }</div>
  
              {/* 3.方式三: &&逻辑与运算 */}
              {/* 场景: 当某一个值, 有可能为undefined时, 使用&&进行条件判断 */}
              <div>{ friend && <div>{friend.name + " " + friend.desc}</div> }</div>
            </div>
          )
        }
      }
  
      // 2.创建root并且渲染App组件
      const root = ReactDOM.createRoot(document.querySelector("#root"))
      root.render(<App/>)
    </script>
  ```

1.react中，方法中this的默认是指向undefined的，

  2.vscode创建用户代码片段
3.
 ```js
   jsx写注释{/* */}
    render(){
       return (
         <div>
           {/*这是一段注释*/}
         </div>
       )
     }
   }
 ```

​    4.

```
 //这三个值无法在jsx中渲染出来
    test1:undefined
    test2:null
    text3:false
```

5.在jsx中对象类型不可以作为子元素,进行直接展示

```html
<div>{this.state.obj}</div>
```

6.jsx嵌入表达式
	运算表达式
	三元运算符
	执行一个函数
	
7.jsx中的class用className
8.jsx中style的写法

```html
<h1 style={{color:"red",fontSzie:"24px"}}>hello world</h1>
```

9.react的事件绑定
	react的事件绑定方法中this默认是undefined
	this的绑定的三种方式

```js
  class App extends React.Component {
      // class fields
      name = "App"

      constructor() {
        //类组件继承一定要加super()并且必须写在最上面
        super()
        this.state = {
          message: "Hello World",
          counter: 100
        }

        this.btn1Click = this.btn1Click.bind(this)
      }

      btn1Click() {
        console.log("btn1Click", this);
        this.setState({ counter: this.state.counter + 1 })
      }

      btn2Click = () => {
        console.log("btn2Click", this)
        this.setState({ counter: 1000 })
      }

      btn3Click() {
        console.log("btn3Click", this);
        this.setState({ counter: 9999 })
      }

      render() {
        const { message } = this.state

        return (
          <div>
            {/* 1.this绑定方式一: bind绑定 */}
            <button onClick={this.btn1Click}>按钮1</button>

            
            {/* 2.this绑定方式二: ES6 class fields */}
            <button onClick={this.btn2Click}>按钮2</button>


            {/* 3.this绑定方式三: 直接传入一个箭头函数(重要) */}
            <button onClick={() => console.log("btn3Click")}>按钮3</button>

            <button onClick={() => this.btn3Click()}>按钮3</button>


            <h2>当前计数: {this.state.counter}</h2>
          </div>
        )
      }
    }

    // 2.创建root并且渲染App组件
    const root = ReactDOM.createRoot(document.querySelector("#root"))
    root.render(<App/>)
```

 10.react事件的传参
 	 

```html
 <button onClick={(e)=>{
          this.click3(e,item,name)
        }}>点击3</button>
```

​        可以用来传递event
​        
 在执行事件函数时，有可能我们需要获取一些参数信息：比如event对象、其他参数
 情况一：获取event对象
 很多时候我们需要拿到event对象来做一些事情（比如阻止默认行为）
 假如我们用不到this，那么直接传入函数就可以获取到event对象；
 情况二：获取更多参数
 有更多参数时，我们最好的方式就是传入一个箭头函数，主动执行的事件函数，并且传入相关的其他参数

```js
  render() {
        const { message } = this.state

        return (
          <div>
            {/* 1.event参数的传递 */}
            <button onClick={this.btnClick.bind(this)}>按钮1</button>
            <button onClick={(event) => this.btnClick(event)}>按钮2</button>

            
            {/* 2.额外的参数传递 */}
            <button onClick={this.btnClick.bind(this, "kobe", 30)}>按钮3(不推荐)</button>

            <button onClick={(event) => this.btnClick(event, "why", 18)}>按钮4</button>
          </div>
        )
      }
```

 11.react的列表渲染使用最多的是使用map
 	过滤filter
 	截取slice
 12.React.createElement() jsx是他的语法糖

​	createElement需要传递三个参数

- type      当前ReactElement的类型，如果是标签元素，那么就使用字符串表示div,如果是组件元素就用组件的名称

- config    所有jsx中的属性在config中都是对象的属性和值的形式存在，例如className作为class

- children  存放在标签中的内容都是以children数组的形式存储

  ![QQ截图20220912114717.png](https://img1.imgtp.com/2022/09/12/pgiZpbQL.png)

 13.render() 将virtual Dom=>Dom
 14.频繁的操作DOM，会产生回流和重绘
 15.React 组件名称首字母必须大写 html标签必须小写
 	类组件需要继承自 React.Component
 	 类组件必须实现render函数
 16.函数式组件
 	 没有生命周期，也会被更新并挂载，但是没有生命周期函数；
 	 没有this(组件实例）；
	 没有内部状态（state）
 17.render函数、函数式组件的返回类型
 		组件，html元素
 		数组、fragments
 		Portals
 		字符串或者数值类型
 		Boolean，null
 18.react的生命周期

生命周期图谱

<img src="https://img1.imgtp.com/2022/09/12/X4cHBQk6.png" alt="QQ截图20220912132938.png" style="zoom:200%;" />

<img src="https://img1.imgtp.com/2022/09/12/ACO5RXp5.png" alt="QQ截图20220912133029.png" style="zoom:200%;" />

 常用的生命周期函数 	

    	1.contructor(){
    	}
         如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。
         constructor中通常只做两件事情：
         通过给 this.state 赋值对象来初始化内部的state；
         为事件绑定实例（this）；
    	2.componentDidMount()
    	  componentDidMount() 会在组件挂载后（插入 DOM 树中）立即调用	
    	  可以进行网络请求，添加订阅，DOM操作
    	3.componentDidUpdate(prevProps, prevState, snapshot)
    	  会在更行后立即调用，首次渲染不会执行，可以使用setState方法，但是必须用于条件语句，当组件更新后，可以在此处对 DOM 进行操作，如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求
    	4.componentWillUnmount()
    	  可以用于清楚timer,取消网络请求，取消订阅
    	5.不常用的生命周期
    	  getDerivedStateFromProps：state 的值在任何时候都依赖于 props时使用；该方法返回一个对象来更新state；
    	  getSnapshotBeforeUpdate：在React更新DOM之前回调的一个函数，可以获取DOM更新前的一些信息（比如说滚动位置）
    	  shouldComponentUpdate：当 props 或 state 发生变化时，shouldComponentUpdate() 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 forceUpdate() 时不会调用该方法。此方法仅作为性能优化的方式而存在。不要企图依靠此方法来“阻止”渲染，因为这可能会产生 bug。你应该考虑使用内置的 PureComponent 组件，而不是手动编写 shouldComponentUpdate()。PureComponent 会对 props 和 state 进行浅层比较，并减少了跳过必要更新的可能性。

   19.组件通信
   	  1.父组件向子组件传递数据 传递参数是常量非string的时候要将其当作变量用{}

```
   	  	<Children2 name="里斯" age={55}></Children2>
```

   	  2.子组件向父组件传递数据，定义回调函数传递给子组件调用

```react
import React, { Component } from 'react'
import AddCounter from './AddCounter'
import SubCounter from './SubCounter'

export class App extends Component {
  constructor() {
    super()

    this.state = {
      counter: 100
    }
  }

  changeCounter(count) {
    this.setState({ counter: this.state.counter + count })
  }

  render() {
    const { counter } = this.state

    return (
      <div>
        <h2>当前计数: {counter}</h2>
        <AddCounter addClick={(count) => this.changeCounter(count)}/>
        <SubCounter subClick={(count) => this.changeCounter(count)}/>
      </div>
    )
  }
}

export default App
```

 

```react
import React, { Component } from 'react'
// import PropTypes from "prop-types"

export class AddCounter extends Component {
  addCount(count) {
    this.props.addClick(count)
  }

  render() {

    return (
      <div>
        <button onClick={e => this.addCount(1)}>+1</button>
        <button onClick={e => this.addCount(5)}>+5</button>
        <button onClick={e => this.addCount(10)}>+10</button>
      </div>
    )
  }
}

// AddCounter.propTypes = {
//   addClick: PropTypes.func
// }

export default AddCounter
```

  	  3.跨组件通信Context，可以使用组件的组合来替代Context		

```
import React from "react"
// 1.创建一个Context并且设置默认值
const ThemeContext = React.createContext({ color: "blue", size: 10 })
export default ThemeContext
```

```
import React from "react"
// 1.创建一个Context
const UserContext = React.createContext()
export default UserContext

```

```react
//App.js
//多个context传递数据的方式
<UserContext.Provider value={{nickname: "kobe", age: 30}}>
          <ThemeContext.Provider value={{color: "red", size: "30"}}>
            <Home {...info}/>
          </ThemeContext.Provider>
 </UserContext.Provider>
```

```react
//类组件的接受方式
import React, { Component } from 'react'
import ThemeContext from './context/theme-context'

export class Profile extends Component {
  render() {
    console.log(this.context)

    return (
      <div>Profile</div>
    )
  }
}

Profile.contextType = ThemeContext

export default Profile
```

```react
//函数式组件的接收方式
import ThemeContext from "./context/theme-context"
function HomeBanner() {
  return <div>
    {/* 函数式组件中使用Context共享的数据 */}
    <ThemeContext.Consumer>
      {
        value => {
          return <h2> Banner theme:{value.color}</h2>
        }
      }
    </ThemeContext.Consumer>
  </div>
}

export default HomeBanner
```

```react
import React, { Component } from 'react'
import ThemeContext from './context/theme-context'
import UserContext from './context/user-context'

export class HomeInfo extends Component {
  //static contextType=ThemeContext 这种写法也可以
  render() {
    // 4.第四步操作: 获取数据, 并且使用数据
    console.log(this.context)

    return (
      <div>
        <h2>HomeInfo: {this.context.color}</h2>
        <UserContext.Consumer>
          {
            value => {
              return <h2>Info User: {value.nickname}</h2>
            }
          }
        </UserContext.Consumer>
      </div>
    )
  }
}

// 3.第三步操作: 设置组件的contextType为某一个Context
HomeInfo.contextType = ThemeContext
HomeInfo.displayName = 'MyDisplayName';//设置在devtool的显示名字
export default HomeInfo
```

```react
//多个context的使用
// Theme context，默认的 theme 是 “light” 值
const ThemeContext = React.createContext('light');
// 用户登录 context
const UserContext = React.createContext({
  name: 'Guest',
});
class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;
    // 提供初始 context 值的 App 组件
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}
function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}
// 一个组件可能会消费多个 context
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

**什么时候使用Context.Consumer呢？

.当使用value的组件是一个函数式组件时；

.当组件中需要使用多个Context时；

 4.使用事件总线
   20.对props进行类型检查 propTypes

```js
import PropTypes from "prop-types"	

Children2.propTypes = {
          // 必填属性
          name: PropTypes.string.isRequired,
          age: PropTypes.number,
          time:PropTypes.array
        };
        // 可以定义默认·值
Children2.defaultProps={
          time:["2022/5/11","2022/5/6"]
        }
```

 21.props为什么不写也是可以的   

```
	 constructor(){
        super()
        this.state={}
      }
   因为react内部会强制性给当前实例复制props
   this.instance=element.props
```

22.组件插槽的实现

```html
 //App.js
<div>
        {/* 1.使用children实现插槽 */}
        <NavBar>
          <button>按钮</button>
          <h2>哈哈哈</h2>
          <i>斜体文本</i>
        </NavBar>

        {/* 2.使用props实现插槽 */}
        <NavBarTwo 
          leftSlot={btn}
          centerSlot={<h2>呵呵呵</h2>}
          rightSlot={<i>斜体2</i>}
        />
      </div>
```

```react
//NavBar.js
export class NavBar extends Component {
  render() {
    const { children } = this.props
    console.log(children)

    return (
      <div className='nav-bar'>
        <div className="left">{children[0]}</div>
        <div className="center">{children[1]}</div>
        <div className="right">{children[2]}</div>
      </div>
    )
  }
}

```

```react
//NavBarTwo.js
import React, { Component } from 'react'
export class NavBarTwo extends Component {
  render() {
    const { leftSlot, centerSlot, rightSlot } = this.props

    return (
      <div className='nav-bar'>
        <div className="left">{leftSlot}</div>
        <div className="center">{centerSlot}</div>
        <div className="right">{rightSlot}</div>
      </div>
    )
  }
}
```

通过children实现的方案虽然可行，但是有一个弊端：通过索引值获取传入的元素很容易出错，不能精准的获取传入的原生

23.setState
   setState是异步更新，可以显著提升性能
   如果每一次调用setState都进行一次更新，那么意味着render函数需要频繁的调用
   最好的办法是获取多个更新，之后进行批量更新
   如果同步更新了state,但是还没执行render函数，那么state和props不能保持同步
   state和props不能保持一致性，会在开发中产生很多问题

   为了拿到异步更新后的数据

​	setState的三种写法

​	

```js
 changeText() {
    // 1.setState更多用法
    // 1.基本使用
    // this.setState({
    //   message: "你好啊, 李银河"
    // })

    // 2.setState可以传入一个回调函数,回调 函数中接收的 state 和 props 都保证为最新。回调函数 的返回值会与 state 进行浅合并。
    // 好处一: 可以在回调函数中编写新的state的逻辑
    // 好处二: 当前的回调函数会将之前的state和props传递进来
    // this.setState((state, props) => {
    //   // 1.编写一些对新的state处理逻辑
    //   // 2.可以获取之前的state和props值
    //   console.log(this.state.message, this.props)
     //获得最新的state和props
	//   console.log(state, props)
    //   return {
    //     message: "你好啊, 李银河"
    //   }
    // })

    // 3.setState在React的事件处理中是一个异步调用
    // 如果希望在数据更新之后(数据合并), 获取到对应的结果执行一些逻辑代码
    // 那么可以在setState中传入第二个参数: callback
    this.setState({ message: "你好啊, 李银河" }, () => {
      console.log("++++++:", this.state.message)
    })
    console.log("------:", this.state.message)
  }
```

##### setState都是异步的吗

在React18以前，在组件的生命周期或者React合成事件中，setState是异步的，在setTimeout以及原生事件中是同步的，react18以后所有都是异步的，如果需要同步操作需要使用flushSync()

```js
  setTimeout(() => {
      // 在react18之前, setTimeout中setState操作, 是同步操作
      // 在react18之后, setTimeout中setState异步操作(批处理)
      flushSync(() => {
        this.setState({ message: "你好啊, 李银河" })
      })
      console.log(this.state.message)
    }, 0);
```

 24.组件创建的时候会被调用一次
 25.render函数，组件更新时调用render函数会让所有子组件都会进行一次更新。使用shouldComponentUpdate这个生命周期函数可以控制state和props发生变化时是否重新调用render函数         

```js
 shouldComponentUpdate(nextProps,nextState){
     //next...是最新的数据
        if(this.state.num!==nextState.num){
          return true
        }
        return false
      }
```

​    在开发中每个组件都写shouldComponentUpdate比较麻烦，可以在使用PureComponent

```
    class Man extends React.PureComponent
```

 26.PureComponent不能用于函数式组件，需要用memo

​	 PureComponent就是对组件进行优化，避免频繁的调用render函数进行渲染，这个方法中调用!shallowEqual(oldProps, newProps) || !shallowEqual(oldState, newState)，进行浅层比较

 27.state中的属性的不可变性
 28.react不建议直接在操作dom，可以使用ref
 	使用ref的三种方式

```react
import React,{createRef} from "react";
class RefDemo extends React.Component{
  constructor(props){
    super(props)
    this.state={}
    this.titleRef=createRef()
    // 第三种使用函数
    this.textInput = null;
  }
  componentDidMount(){
   
  }
  componentWillUnmount(){
    
  }
  changeRef1(){
    // 方式一已经被淘汰了
    this.refs.refTitle.innerHTML="hello ref"
  }
  changeRef2(){
    // 官方推荐以这种方式
     this.titleRef.current.innerHTML="hello big"
    console.log(this.titleRef.current)
  }
  focusTextInput(){
    this.textInput.focus()
    console.log(this.textInput)
  }
  render(){
    return (
      <div>
        <h1 ref="refTitle">hello react</h1>
        <button onClick={()=>this.changeRef1()}>按钮1</button>
        <h1 ref={this.titleRef}>hello world</h1>
        <button onClick={()=>this.changeRef2()}>按钮2</button>
        <button onClick={()=>this.focusTextInput()}>focus</button>
        <div><input ref={element=>this.textInput=element} type="text" /></div>
      </div>
    )
  }
}


export default RefDemo
```

函数式组件中没有ref属性，要使用const textInput = useRef(null);
可以通过ref访问子组件的方法和属性和vue一样

#### key的注意事项

key必须唯一

key不能够使用随机数（因为随机数在下一次render时会重新生成一个数字）

使用index作为key,对性能没有优化

26.受控组件

​	受控组件就是通过setState来控制值，state成为唯一的数据源

​	

```js
 inputChange(event) {
    console.log("inputChange:", event.target.value)
    this.setState({ username: event.target.value })
  }

  render() {
    const { username } = this.state

    return (
      <div>
        {/* 受控组件 */}
        <input type="checkbox" value={username} onChange={e => this.inputChange(e)}/>

        {/* 非受控组件 */}
        <input type="text" />
        <h2>username: {username}</h2>
      </div>
    )
  }
```

当type为text/textarea/select的时候绑定的是value,需要使用事件onChange

当type为checkbox/radio的时候，绑定的是checked，使用onChange事件

```
<input type="file" />始终是非受控组件
```

使用form进行表单提交

```js
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('你喜欢的风味是: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          选择你喜欢的风味:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">葡萄柚</option>
            <option value="lime">酸橙</option>
            <option value="coconut">椰子</option>
            <option value="mango">芒果</option>
          </select>
        </label>
        <input type="submit" value="提交" />
      </form>
    );
  }
}
```

当针对多个输入的时候

可以给每个input绑定唯一的name,然后利用计算属性名来进行赋值操作

```react
handleSubmitClick(event) {
    // 1.阻止默认的行为
    event.preventDefault()

    // 2.获取到所有的表单数据, 对数据进行组件
    console.log("获取所有的输入内容")
    console.log(this.state.username, this.state.password)

    // 3.以网络请求的方式, 将数据传递给服务器(ajax/fetch/axios)
  }

  // handleUsernameChange(event) {
  //   this.setState({ username: event.target.value })
  // }

  // handlePasswordChange(event) {
  //   this.setState({ password: event.target.value })
  // }

  handleInputChange(event) {
    this.setState({
      [event.target.name]: event.target.value
    })
  }

  render() {
    const { username, password } = this.state

    return (
      <div>
        <form onSubmit={e => this.handleSubmitClick(e)}>
          {/* 1.用户名和密码 */}
          <label htmlFor="username">
            用户: 
            <input 
              id='username' 
              type="text" 
              name='username' 
              value={username} 
              onChange={e => this.handleInputChange(e)}
            />
          </label>
          <label htmlFor="password">
            密码: 
            <input 
              id='password' 
              type="password" 
              name='password' 
              value={password} 
              onChange={e => this.handleInputChange(e)}
            />
          </label>

          <button type='submit'>注册</button>
        </form>
      </div>
    )
  }
```

27.非受控组件

非受控组件就是将表单数据交给DOM节点来处理，

```react
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);
    event.preventDefault();
  }

  render() {、
  //defaultValue是默认值
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input  defaultValue="Bob" type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

```
<input type="checkbox"> 和 <input type="radio"> 支持 defaultChecked，<select> 和 <textarea> 支
持 defaultValue。
```

28.高阶组件

高阶组件是一个函数，高阶组件的参数是一个组件，返回值也是一个组件。高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。HOC 是纯函数，没有副作用，高阶组件适用于抽取公共的逻辑

```react
import { PureComponent } from 'react'
//props增强
// 定义组件: 给一些需要特殊数据的组件, 注入props
function enhancedUserInfo(OriginComponent) {
  class NewComponent extends PureComponent {
    constructor(props) {
      super(props)
      this.state = {
        userInfo: {
          name: "coderwhy",
          level: 99
        }
      }
    }
    render() {
      return <OriginComponent {...this.props} {...this.state.userInfo}/>
    }
  }
  return NewComponent
}
export default enhancedUserInfo
```

```
//我们也可以利用高阶函数来劫持生命周期，在生命周期中完成自己的逻辑
import { PureComponent } from "react";
function logRenderTime(OriginComponent) {
  return class extends PureComponent {
    UNSAFE_componentWillMount() {
      this.beginTime = new Date().getTime()  
    componentDidMount() {
      this.endTime = new Date().getTime()
      const interval = this.endTime - this.beginTime
      console.log(`当前${OriginComponent.name}页面花费了${interval}ms渲染完成!`)
    }
    render() {
      return <OriginComponent {...this.props}/>
    }
  }
}
export default logRenderTime
```

```react
//渲染鉴权，可以判断用户有没有登录，如果没有登录则渲染其他组件
function loginAuth(OriginComponent) {
  return props => {
    // 从localStorage中获取token
    const token = localStorage.getItem("token")
    if (token) {
      return <OriginComponent {...props}/>
    } else {
      return <h2>请先登录, 再进行跳转到对应的页面中</h2>
    }
  }
}
export default loginAuth

```

```react
import ThemeContext from "../context/theme_context"
//和context结合
function withTheme(OriginComponment) {
  return (props) => {
    return (
      <ThemeContext.Consumer>
        {
          value => {
            return <OriginComponment {...value} {...props}/>
          }
        }
      </ThemeContext.Consumer>
    )
  }
}
export default withTheme
```

不要在 render 方法中使用 HOC

HOC也有自己的一些缺陷：

 HOC需要在原组件上进行包裹或者嵌套，如果大量使用HOC，将会产生非常多的嵌套，这让调试变得非常困难； HOC可以劫持props，在不遵守约定的情况下也可能造成冲突；

Hooks的出现，是开创性的，它解决了很多React之前的存在的问题

比如this指向问题、比如hoc的嵌套复杂度问题等等

29.ref的转发，获取函数式组件内部的ref 可以使用forwardRef高阶函数
30.Portals的使用，用于将渲染元素独立于父组件，渲染到其他元素之上。使用React.createPortal(child,container)
31.fragment的使用，相当于vue的template在实际的dom中不渲染，带key的时候不能使用短语法
	还可以使用短语发    

```
    class Columns extends React.Component {
      render() {
        return (
          <>
            <td>Hello</td>
            <td>World</td>
          </>
        );
      }
    }
```

 32.StrictMode 开启react的严格模式，只有开发环境下起作用

​	StrictMode是用来突显应用程序中的潜在问题的工具

​	它为其后代元素触发额外的检查和警告

 	可以识别不安全的生命周期
 	使用过时的ref的API
 	检测意外的副作用。

​			组件的constructor会调用两次

​			生产环境终不悔调用两次

​	j检测使用废弃的findDOMNode方法

 	检测过时的context api 

​			早期的Context是通过static属性声明Context对象属性，通过getChildContext返回Context对象等方式来使用Context的

 33.react中的css
 	1.内联样式

​			style 接受一个采用小驼峰命名属性的 JavaScript 对象，，而不是 CSS 字符串

​			并且可以引用state中的状态来设置相关的样式

​			.内联样式, 样式之间不会有冲突

​			 可以动态获取当前state中的状态

​			 写法上都需要使用驼峰标识

​			某些样式没有提示

​			大量的样式, 代码混乱

​			某些样式无法编写(比如伪类/伪元素

 	2.css modules
 		不是react特有的决绝方案，所有类似webpack配置环境都可以使用，在react中已经配置，在其他项目中使用需要在webpack.config.js中module:true
 	 	要创建xxx.moudle.css文件
 	 	不能使用连接符创建类名(.home-title)，js不支持，需要使用{引入名字style.className}来编写
 	 	不方便更改样式.不方便动态修改某些样式，依然需要使用内联样式
 	 3.css in js
 	 	常用的 css in js库
 	 		styled-components
 	 			在多人协作中，css必定会出现命名冲突，与vue的scoped解决方案不同，react用styled-					components的给类名加了随机字符的方式实现了css的私有化，它也是全局的
 	 		styled-components其实是一个组件，可以传入props
 	 		emotion
 	 		glamorous

​		styled-components的本质是通过函数的调用，最终创建出一个组件，这个组件会自动添加上一个不重复的class

​		styled-components会给该class添加相关的样式，它支持css预处理器一样嵌套，支持直接子代选择器或者后代选择器，并且编写样式，可以通过&符号获取当前元素，直接伪类选择器，伪元素等

```js
import styled from "styled-components"
import {
  primaryColor,
  largeSize
} from "./style/variables"

// 1.基本使用
export const AppWrapper = styled.div`
  .footer {
    border: 1px solid orange;
  }
// const obj = {
//   name: (props) => props.name || "why"
// }
// 2.子元素单独抽取到一个样式组件
// 3.可以接受外部传入的props
// 4.可以通过attrs给标签模板字符串中提供的属性
// 5.从一个单独的文件中引入变量
export const SectionWrapper = styled.div.attrs(props => ({
  tColor: props.color || "blue"
}))`
  border: 1px solid red;

  .title {
    font-size: ${props => props.size}px;
    color: ${props => props.tColor};

    &:hover {
      background-color: purple;
    }
  }
  .content {
    font-size: ${largeSize}px;
    color: ${primaryColor};
  }
`
```

```js
import styled from "styled-components";
const HYButton = styled.button`
  border: 1px solid red;
  border-radius: 5px;
`
// css样式的继承
export const HYButtonWrapper = styled(HYButton)`
  background-color: #0f0;
  color: #fff;
`
export const HomeWrapper = styled.div`
  .top {
    .banner {
      color: red;
    }
  }
  .bottom {
    .header {
      color: ${props => props.theme.color};
      font-size: ${props => props.theme.size};
    }

    .product-list {
      .item {
        color: blue;
      }
    }
  }
`
```

 	4.普通的csc会相互层叠，都会合并到全局的css中去，遭成影响到其他模块
 	5.less的编写方式  需要配置webpack
 	  需要使用craco  目前针对creat-react-app 5 的配置 npm i @craco/craco@alpha

```json
 "scripts": {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test",
    "eject": "react-scripts eject"
  },
```

​	

```js
//craco.config.js
const CracoLessPlugin = require('craco-less');
module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
};

```

#### React中添加class

可以借助第三方库classnames

 34.axios中，axios({})这样是使用他默认创建的实例，如果要自己创建一个实例要使用axios.create({})

####  react-transition-group

  这个库可以帮助我们方便的实现组件的 入场 和 离场 动画，使用时需要进行额外的安装

```
npm install react-transition-group --save
yarn add react-transition-group
```

react-transition-group主要包括四个组件

Tansition

​		该组件是一个和平台无关的组件（不一定要结合CSS）

​		在前端开发中，我们一般是结合CSS来完成样式，所以比较常用的是CSSTransition

CSSTransition

​		在前端开发中，通常使用CSSTransition来完成过渡动画

​		CSSTransition是基于Transition组件构建的

​		CSSTransition执行过程中，有三个状态：appear、enter、exit；

​		它们有三种状态，需要定义对应的CSS样式：

​		第一类，开始状态：对于的类是-appear、-enter、exit； 

​		第二类：执行动画：对应的类是-appear-active、-enter-active、-exit-active； 

​        第三类：执行结束：对应的类是-appear-done、-enter-done、-exit-done；

​		CSSTransition常用属性

​		in

​		classNames

​		timeout

​		appear

​		unmountOnExit

###### 		钩子函数:

​				onEnter

​				onEntering

​				onEntered

```react
import React, { createRef, PureComponent } from 'react'
import { CSSTransition } from "react-transition-group"
import "./style.css"
export class App extends PureComponent {
  constructor(props) {
    super(props)
    this.state = {
      isShow: true
    }
    this.sectionRef = createRef()
  }
  render() {
    const { isShow } = this.state
    return (
      <div>
        <button onClick={e => this.setState({isShow: !isShow})}>切换</button>
        {/* { isShow && <h2>哈哈哈</h2> } */}
        <CSSTransition 
          nodeRef={this.sectionRef}
          in={isShow} 
          unmountOnExit={true} 
          classNames="why" 
          timeout={2000}
          appear
          onEnter={e => console.log("开始进入动画")}
          onEntering={e => console.log("执行进入动画")}
          onEntered={e => console.log("执行进入结束")}
          onExit={e => console.log("开始离开动画")}
          onExiting={e => console.log("执行离开动画")}
          onExited={e => console.log("执行离开结束")}
        >
          <div className='section' ref={this.sectionRef}>
            <h2>哈哈哈</h2>
            <p>我是内容, 哈哈哈</p>
          </div>
        </CSSTransition>
      </div>
    )
  }
}
export default App
```

```css
.why-appear, .why-enter {
  opacity: 0;
}
.why-appear-active, .why-enter-active {
  opacity: 1;
  transition: opacity 2s ease;
}
/* 离开动画 */
.why-exit {
  opacity: 1;
}

.why-exit-active {
  opacity: 0;
  transition: opacity 2s ease;
}
```

SwitchTransition

​		两个组件显示和切换时，使用该组件

​		SwitchTransition中主要有一个属性：mode，有两个值

​			in-out：表示新组件先进入，旧组件再移除；

​			out-in：表示就组件先移除，新组建再进入

​		SwitchTransition组件里面要有CSSTransition或者Transition组件，不能直接包裹你想要切换的组件

​		SwitchTransition里面的CSSTransition或Transition组件不再像以前那样接受in属性来判断元素是何种状态，取而代之的是 key属性

```react
import React, { PureComponent } from 'react'
import { SwitchTransition, CSSTransition } from 'react-transition-group'
import "./style.css"
export class App extends PureComponent {
  constructor() {
    super() 
    this.state = {
      isLogin: true
    }
  }
  render() {
    const { isLogin } = this.state

    return (
      <div>
        <SwitchTransition mode='out-in'>
          <CSSTransition
            key={isLogin ? "exit": "login"}
            classNames="login"
            timeout={1000}
          >
            <button onClick={e => this.setState({ isLogin: !isLogin })}>
              { isLogin ? "退出": "登录" }
            </button>
          </CSSTransition>
        </SwitchTransition>
      </div>
    )
  }
}
export default App
```

```css
.login-enter {
  transform: translateX(100px);
  opacity: 0;
}

.login-enter-active {
  transform: translateX(0);
  opacity: 1;
  transition: all 1s ease;
}

.login-exit {
  transform: translateX(0);
  opacity: 1;
}

.login-exit-active {
  transform: translateX(-100px);
  opacity: 0;
  transition: all 1s ease;
}
```

TransitionGroup

​		将多个组件包裹在其中，一般用于列表中的元素的动画

​		◼ 当我们有一组动画时，需要将这些CSSTransition放入到一个TransitionGroup中来完成动画：

```react
 <TransitionGroup component="ul">
          {
            books.map((item, index) => {
              return (
                <CSSTransition key={item.id} classNames="book" timeout={1000}>
                  <li>
                    <span>{item.name}-{item.price}</span>
                    <button onClick={e => this.removeBook(index)}>删除</button>
                  </li>
                </CSSTransition>
              )
            })
          }
        </TransitionGroup>
```

 

```css
.book-enter {
  transform: translateX(150px);
  opacity: 0;
}

.book-enter-active {
  transform: translateX(0);
  opacity: 1;
  transition: all 1s ease;
}

.book-exit {
  transform: translateX(0);
  opacity: 1;
}

.book-exit-active {
  transform: translateX(150px);
  opacity: 0;
  transition: all 1s ease;
}

```

36.react纯函数
 37.node中对ES6模块化的支持 在13.2.0以后对es6的支持，需要在package.json中添加属性"type":"module",导入文件时要跟上.js后缀名
 38.redux
 	redux主要是负责状态管理
 	store:createStore(reducer,..)
 	state:通过store.getState()来获取当前state
 	react的组成
 		action:是用来更新数据，所有的数据变化，必须通过dispatch来派发action来更新，action可以是一个对象，也可以是一个函数，函数必须返回一个对象
 		reducer：是将state和action连接起来，reducer是一个纯函数，reducer是将传入的reducer和action整和成为一个新的state
 		dispatch:用来派发action
 	redux的三大原则
 		1.单一数据源
 		2.state是只读的
 		3.使用纯函数来执行修改
 39.单向数据流
 40.react动画中<TransitionGroup>中需要再用<cssTransition>包裹,否则会报错
 41.react-router
 	默认是模糊匹配
 42.react-hook
 	import React from "react"//这个是在函数式组件和类组件都要写，因为jsx是React.createElement()的语法糖，会在内部调用
 	useState()
 	Hook的使用规则：
 		只能在函数最外层调用Hook,不要在循环，条件判断或者子函数中调用
 		只能在React的函数组件中调用Hook,不要再其他js函数中使用
 		
 	 const [count, setCount] = useState(() => 10);//useState可以传入一个函数

  console.log("CounterHook渲染");

  function handleBtnClick() {
    // setCount(count + 10);


​    setCount((prevCount) => prevCount + 10);//preCount是上一个count
​    
​    如果你的更新函数返回值与当前 state 完全相同，则随后的重渲染会被完全跳过。
​    与 class 组件中的 setState 方法不同，useState 不会自动合并更新对象。你可以用函数式的 	setState 结合展开运算符来达到合并更新对象的效果。
​    setState(prevState => {
​      // 也可以使用 Object.assign
​      return {...prevState, ...updatedValues};
​    });
​    useReducer 是另一种可选方案，它更适合用于管理包含多个子值的 state 对象。


 43.useEffect()
 	useEffect(() => {
    console.log("订阅一些事件");

​    return () => {
​      console.log("取消订阅事件")
​    }
  }, []);//通过返回一个函数来执行类似componmentwillumnount来清除副作用，[]这个可以作为优化，只有在组件切换时才执行，[]只会执行一次，相当于是componentDiMount,componentWillUnmount
  44.useEffect第二个参数
   useEffect(() => {
​    console.log("修改DOM", count);
  }, [count]);//useEffect可以让某个属性发生改变时才执行，这个属性必须在useEffect的回调函数中有使用到
  如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。

使用多个useEffect可以分开使用，执行的时候会挨个执行

#### 45.useContext的使用

​	useContext可以让多个Context传递给函数式组件的时候减少consumer的嵌套

```js
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <UserContext.Provider value={{name: "why", level: 99}}>
    <TokenContext.Provider value={'coderwhy'}>
      <App />
    </TokenContext.Provider>
  </UserContext.Provider>
);
```

```js
import React, { memo, useContext } from 'react'
import { UserContext, ThemeContext } from "./context"

const App = memo(() => {
  // 使用Context
  const user = useContext(UserContext)
  const theme = useContext(ThemeContext)

  return (
    <div>
      <h2>User: {user.name}-{user.level}</h2>
      <h2 style={{color: theme.color, fontSize: theme.size}}>Theme</h2>
    </div>
  )
})

export default App
```

#### 46.useCallback的使用

useReducer不是redux的替代品，useReducer仅仅是useState的一种替代方案。在某些场景下，如果state的处理逻辑比较复杂，我们可以通过useReducer来对其进行拆分，或者这次修改的state需要依赖之前的state时，也可以使用。

```js
function reducer(state, action) {
  switch(action.type) {
    case "increment":
      return { ...state, counter: state.counter + 1 }
    case "decrement":
      return { ...state, counter: state.counter - 1 }
    case "add_number":
      return { ...state, counter: state.counter + action.num }
    case "sub_number":
      return { ...state, counter: state.counter - action.num }
    default:
      return state
  }
}
```

```html
 const [state, dispatch] = useReducer(reducer, { counter: 0, friends: [], user: {} })

      <button onClick={e => dispatch({type: "increment"})}>+1</button>
      <button onClick={e => dispatch({type: "decrement"})}>-1</button>
      <button onClick={e => dispatch({type: "add_number", num: 5})}>+5</button>
      <button onClick={e => dispatch({type: "sub_number", num: 5})}>-5</button>
      <button onClick={e => dispatch({type: "add_number", num: 100})}>+100</button>
```



####   47.useMemo的使用

在进行大量的计算的时候，使用useMemo可以避免重复的渲染

可以对子组件传递相同内容的对象的时候避免重新渲染

```js
import React, { memo, useCallback } from 'react'
import { useMemo, useState } from 'react'


const HelloWorld = memo(function(props) {
  console.log("HelloWorld被渲染~")
  return <h2>Hello World</h2>
})


function calcNumTotal(num) {
  // console.log("calcNumTotal的计算过程被调用~")
  let total = 0
  for (let i = 1; i <= num; i++) {
    total += i
  }
  return total
}

const App = memo(() => {
  const [count, setCount] = useState(0)

  // const result = calcNumTotal(50)

  // 1.不依赖任何的值, 进行计算
  const result = useMemo(() => {
    return calcNumTotal(50)
  }, [])

  // 2.依赖count
  // const result = useMemo(() => {
  //   return calcNumTotal(count*2)
  // }, [count])

  // 3.useMemo和useCallback的对比
  function fn() {}
  // const increment = useCallback(fn, [])
  // const increment2 = useMemo(() => fn, [])


  // 4.使用useMemo对子组件渲染进行优化
  // const info = { name: "why", age: 18 }
  const info = useMemo(() => ({name: "why", age: 18}), [])

  return (
    <div>
      <h2>计算结果: {result}</h2>
      <h2>计数器: {count}</h2>
      <button onClick={e => setCount(count+1)}>+1</button>

      <HelloWorld result={result} info={info} />
    </div>
  )
})

export default App
```

48.useCallback是针对回调函数进行优化，useMemo是对返回值进行优化。userCallback可以用于传递给被pureComponent或者memo包裹的子组件的函数

```js
import React, { memo, useState, useCallback, useRef } from 'react'

// useCallback性能优化的点:
// 1.当需要将一个函数传递给子组件时, 最好使用useCallback进行优化, 将优化之后的函数, 传递给子组件
//如果没有使用usecallback，函数式组件在发生更新的时候，所有代码都会重新执行，传递给子组件的函数会重新生成，如果使用usecallback,会根据第二个参数数组中的值，是否生成新的函数，使子组件是否重新渲染
// props中的属性发生改变时, 组件本身就会被重新渲染，
const HYHome = memo(function(props) {
  const { increment } = props
  console.log("HYHome被渲染")
  return (
    <div>
      <button onClick={increment}>increment+1</button>

      {/* 100个子组件 */}
    </div>
  )
})

const App = memo(function() {
  const [count, setCount] = useState(0)
  const [message, setMessage] = useState("hello")

  // 闭包陷阱: useCallback  解决闭包陷阱方式一，但是会生成新的函数
  // const increment = useCallback(function foo() {
  //   console.log("increment")
  //   setCount(count+1)
  // }, [count])

  // 进一步的优化: 当count发生改变时, 也使用同一个函数(了解)
  // 做法一: 将count依赖移除掉, 缺点: 闭包陷阱,因为此时的foo和原来的foo不是同一个foo,而原来的foo依然使用之前的count
  // const increment = useCallback(function foo() {
  //   console.log("increment")
  //   setCount(count+1)
  // }, [])

  // 做法二: useRef, 在组件多次渲染时, 返回的是同一个值
  const countRef = useRef()
  countRef.current = count
  const increment = useCallback(function foo() {
    console.log("increment")
    setCount(countRef.current + 1)
  }, [])

  // 普通的函数
  // const increment = () => {
  //   setCount(count+1)
  // }

  return (
    <div>
      <h2>计数: {count}</h2>
      <button onClick={increment}>+1</button>

      <HYHome increment={increment}/>

      <h2>message:{message}</h2>
      <button onClick={e => setMessage(Math.random())}>修改message</button>
    </div>
  )
})


// function foo(name) {
//   function bar() {
//     console.log(name)
//   }
//   return bar
// }

// const bar1 = foo("why")
// bar1() // why
// bar1() // why

// const bar2 = foo("kobe")
// bar2() // kobe

// bar1() // why

export default App
```

useCallback会返回一个函数的memoized(记忆的)，在依赖不变的情况下，多次定义的时候，返回的值是相同的。通常使用useCallback的目的是不希望子组件进行多次渲染，并不是为了函数继续缓存

#### useRef

useRef返回一个ref对象，返回的ref对象再组件的整个生命周期保持不变，会指向同一个对象

useRef的两种用法：

​	用法一：引入DOM(或者组件，但是需要class组件)元素

​	用法二： 保存一个数据，这个对象在整个生命周期可以保持不变

49.Refs的转发
  	就是父组件想获取到子组件的ref,在高阶组件中转发refs
  	可以使用React.forwardRef()
  	const ref=useRef(initialValue)//初始化值后不会发生改变	

```
export default function RefHookDemo02() {
  const [count, setCount] = useState(0);

  const numRef = useRef(count);

  useEffect(() => {
    numRef.current = count;
  }, [count])

  return (

    <div>
      {/* <h2>numRef中的值: {numRef.current}</h2>
      <h2>count中的值: {count}</h2> */}
      <h2>count上一次的值: {numRef.current}</h2>
      <h2>count这一次的值: {count}</h2>
      <button onClick={e => setCount(count + 10)}>+10</button>
    </div>

  )
}//useEffect和useRef结合使用可以修改numRef.current
```

50.useImperativeHandle要和React.forwardRef()一起使用

通过forwardRef可以将ref转发到子组件，获取到子组件内部的ref,子组件拿到父组件创建的ref绑定到某个元素上，forwordRef会将子组件的DOM暴露给父组件，父组件拿到DOM之后可以任意操作，可以使用useImperativeHandle暴露固定的操作

```js
import React, { memo, useRef, forwardRef, useImperativeHandle } from 'react'

const HelloWorld = memo(forwardRef((props, ref) => {

  const inputRef = useRef()

  // 子组件对父组件传入的ref进行处理
  useImperativeHandle(ref, () => {
    return {
      focus() {
        console.log("focus")
        inputRef.current.focus()
      },
      setValue(value) {
        inputRef.current.value = value
      }
    }
  })

  return <input type="text" ref={inputRef}/>
}))


const App = memo(() => {
  const titleRef = useRef()
  const inputRef = useRef()

  function handleDOM() {
    // console.log(inputRef.current)
    inputRef.current.focus()
    // inputRef.current.value = ""
    inputRef.current.setValue("哈哈哈")
  }

  return (
    <div>
      <h2 ref={titleRef}>哈哈哈</h2>
      <HelloWorld ref={inputRef}/>
      <button onClick={handleDOM}>DOM操作</button>
    </div>
  )
})

export default App

```

51.自定义hook：就是将react的hook封装成函数
52.fiber的原理
53.不要在条件表达式中使用hook
54.使用normailze.css对项目进行初始化
	使用@craco/craco修改react 配置
		修改webpack的alias
	使用react-config-router进行路由配置
	使用styled-compoments进行写css样式
		background:url(${reuire()})//需要使用require来引入图片
		搜索框使用antDesign
		数据都是用redux来管理
		用redux-thunk进行异步请求
		配置redux-devtool
		将各个模块的redux分开写再各个文件中，然后进行合并，使用combineReducers
		store使用Provider进行传递
		所有组件用memo()包裹管理
		使用redux的hook:useDispatch()
					   useSelector(state=>({}),shallowEqual)
					   shallowEqual//进行浅层比较,做优化
		Immutablejs可以解决使用拷贝来决绝数据可变性的问题，带来的性能问题		

```
const im=immutable
			const info={}
			const imfoIM=im(info)
			imfoIm.set()
			imfoIM.get()
			imfoIm.list()
			imfoim.fromJS()//深层次转换
```

​		2.只能再react函数中调用成immutable类型 
​			state.getIn(["a","b"])//相当于a.b
​			styled-components的传参
​			使用useCallback对需要传给子组件的函数进行包裹，可以有缓存，并提高性能
​			
​			使用 redux-immutable中的commineReducer来优化combineReducer,提高性能

55 Hook的本质就是javascript函数，他要遵循两条规则
	1.只在最顶层使用hook
		不要再循环，条件或者嵌套函数中调用hook,确保总是再你的react函数的最顶层去调用他们
		遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。这让 React 能够在多次的 useState 和 useEffect 调用之间保持 hook 状态的正确
	2.只能再react函数中调用Hook
	  不要在普通的javascript中调用hook,可以在reat函数组件中调用hook,也可以在在定义hook中调用其他hook
56.自定义HOOK一定要以use开头
57.在两个组件中使用相同的 Hook 会共享 state 吗？不会。自定义 Hook 是一种重用状态逻辑的机制(例如设置为订阅并存储当前值)，所以每次使用自定义 Hook 时，其中的所有 state 和副作用都是完全隔离的。
58.自定义 Hook 如何获取独立的 state？每次调用 Hook，它都会获取独立的 state
	由于我们直接调用了 useFriendStatus，从 React 的角度来看，我们的组件只是调用了 useState 和 useEffect
59.惰性初始化state

`initialState` 参数只会在组件的初始渲染中起作用，后续渲染时会被忽略。如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state，此函数只在初始渲染时被调用：

```
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

#### 60.useEffect是在每一轮渲染结束后执行

61.组件卸载时需要清除effect创建的订阅，定时器等，useEffect函数需要返回一个清理函数

```
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => {
    // 清除订阅
    subscription.unsubscribe();
  };
});
```

为防止内存泄漏，清除函数会在组件卸载之前执行。另外，如果组件多次渲染，则会在子啊个effect之前，上一个effect就会被清除

###### 62.effect的执行时机

与componentDidMount,cimponentDidUpadate不同的是，在浏览器完成布局与绘制之后，

传给useEffect的函数会延迟调用，这使得它适用于许多常见的副作用场景，比如设置订阅和事件处理情况，因此不应在函数值执行阻塞浏览器更新的操作

###### 63.useContext

```
const MyContext= React.createContext(value)

const value = useContext(MyContext);

```

myContext是React.createContext的返回值，并返回该context的当前值，当前的value值由

他的上层组件距离当前组件最近的<MyContext.Provider>的value prop决定的

当Provider中的value发生更新的时候，useContext就会出发重新渲染，即使组件时使用React.emo包裹，或者使用shouldComponentUpdat生命周期，该组件都会重新渲染

只要MyConetext里面的值发生改变，使用了useContext的组件都会重新渲染，如果重新渲染开销大，可以使用memoization来优化

useContext(MyContext)===>class中的static contextType=MyContext或者<MyContext.Consumer>

`useContext(MyContext)` 只是让你能够*读取* context 的值以及订阅 context 的变化。你仍然需要在上层组件树中使用 `<MyContext.Provider>` 来为下层组件*提供* context。

###### 64.`useReducer`

```
const [state, dispatch] = useReducer(reducer, initialArg, init);

useState的替代方案
在某些场合会比useState更加适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。并且，使用 useReducer 还能给那些会触发深更新的组件做性能优化，因为你可以向子组件传递 dispatch 而不是回调函数 。
```

```
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

#### 惰性初始化

useReducer的第三个参数传入一个函数，这样初始state将设置为传入函数的返回值

可以通过外部来传入

```
function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}

```

如果 Reducer Hook 的返回值与当前 state 相同，React 将跳过子组件的渲染及副作用的执行。（React 使用 [`Object.is` 比较算法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 来比较 state。）

如果你在渲染期间执行了高开销的计算，则可以使用 `useMemo` 来进行优化。

useReducer不可以作为redux的替代方案，不可以进行数据共享

65.使用memo包裹函数，可以进行性能优化，会对props进行浅层比较，如果props没有发生更新，则不会重新渲染

66.没有优化以前。父组件发生改变子组件也会重新渲染

67.useCallback

返回一个 [memoized](https://en.wikipedia.org/wiki/Memoization) 回调函数。

把内联回调函数及依赖项数组作为参数传入 `useCallback`，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 `shouldComponentUpdate`）的子组件时，它将非常有用。

![QQ截图20220620112711.png](https://img1.imgtp.com/2022/09/12/JXSSiW1v.png)

![QQ截图20220620112901.png](https://img1.imgtp.com/2022/09/12/7N1MVzzM.png)

68.useMeno

​	`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。

useCallback是返回[memoized](https://en.wikipedia.org/wiki/Memoization) 回调函数。useMeno是返回一个memoized值

useMemo只会在依赖项发生改变时才会重新计算执行，返回新的值，避免每次渲染时都进行高开销的计算

记住，传入 `useMemo` 的函数会在渲染期间执行。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo`。

如果没有提供依赖项数组，`useMemo` 在每次渲染时都会计算新的值

![QQ截图20220620131653.png](https://img1.imgtp.com/2022/09/12/YMPGqZBu.png)

![QQ截图20220620131701.png](https://img1.imgtp.com/2022/09/12/9Z0ICdSV.png)

69.useRef

useRef返回一个ref对象，返回的ref对象在组件的整个生命周期保持不变

最常用的ref是两种用法

​	1.引入DOM(或者是class组件)元素，不可以用在函数组件中

​	2.保存一个数据，这个对象在整个生命周期中可以保持不变

![QQ截图20220620143141.png](https://img1.imgtp.com/2022/09/12/dwdJVSSg.png)

![QQ截图20220620143158.png](https://img1.imgtp.com/2022/09/12/emUNGaLF.png)

70.`useImperativeHandle`

useImperativeHandle要和forwardRef结合使用，使用useImperativeHandle可以使父元素不可以对子组件中的ref进行随意操作

![QQ截图20220620143648.png](https://img1.imgtp.com/2022/09/12/Ey8AqNSp.png)

![QQ截图20220620143735.png](https://img1.imgtp.com/2022/09/12/crDRrvA5.png)

71.useLayoutEffect

​	和useEffect的区别是

​	useEffect会在渲染的内容更新到DOM之后再执行，而不会阻塞DOM的更新

​	useLayoutEffect会在渲染的内容更新到DOM上之前执行，会阻塞DOM的更新

​	![QQ截图20220620144244.png](https://img1.imgtp.com/2022/09/12/Zgl2puzy.png)

![QQ截图20220620144253.png](https://img1.imgtp.com/2022/09/12/a6VmoNdR.png)

72.redux-thunk

​	 使用中间件目的是再dispatch的action和reducer之间扩展自己的代码，例如日志记录，调用异步接口，添加代码调试功能等等

​	可以使用redux-thunk发送异步请求

​	1.通常情况下。dispatch(action),actionn余姚是一个javascript对象

​	2.redux-thunk可以让dispatch(action函数)，action可以是一个函数

​	3.这个函数被调用的时候，会返回一个函数并给这个函数传一个patch,getState函数

​				dispatch函数用于再次派发action

​				getState函数获取到之前的状态

![](C:\Users\10152\Desktop\study\memo\React\QQ截图20220620161818.png)

![QQ截图20220620162945.png](https://img1.imgtp.com/2022/09/12/DaBjyVoU.png)

###### 73.redux-devtools

是用来对redux中的状态进行跟踪调试

![QQ截图20220620163435.png](https://img1.imgtp.com/2022/09/12/QsLGVWW4.png)

##### 74.combineReducers

redux中提供了一个函数combineReducers来合并多个reducer

combinerReducer的实现

​	它会将reducer合并，并且返回一个combation函数相当于是新的reducer

​	在执行combination函数的过程中，它会通过判断前后返回的数据是否相同来决定返回之前的state还是新的state；

   新的state会触发订阅者发生对应的刷新，而旧的state可以有效的组织订阅者发生刷新；

![QQ截图20220620163958.png](https://img1.imgtp.com/2022/09/12/lfwbA1yr.png)

##### 75.useSelector()

```
import React from 'react'
import { shallowEqual, useSelector } from 'react-redux'

export const CounterComponent = () => {
  const counter = useSelector((state) => state.counter,shallowEqual)
  return <div>{counter}</div>
}
//在hook中使用redux,使用useSlectore来获取state
```

##### 76.`useDispatch()`

```
const dispatch = useDispatch()//派发action
```

```
export const Todos = () => {
  const dispatch = useDispatch()

  useEffect(() => {
    dispatch(fetchTodos())
    // Safe to add dispatch to the dependencies array
  }, [dispatch])
}
```

##### 77.`useStore()`

替代createStore()
