---
title: react-router-v6笔记
date: 2022-09-08 13:58:20
tags:
categories:
- [react, note]
---

```
1.前端路由的原理
	当url发生变化的时候，引起页面不刷新来修改页面的内容的两个方法
		1.通过修改url中的hash的变化来修改页面的内容
			window.onhashchange=function(){
				switch(location.hash){
					case "#/a":
					div.innerHTML=""
					break;
				}
			}
		//url的hash也就是锚点。本质上就是修改了window.location的href
		//可以通过直接赋值location.hash来改变href.但是页面不会刷新，
		//但是hash会带有#
		2.可以使用HTML5的history模式来修改url,他有六种模式来改变URL的变化而不刷新页面
			history.replactState():替换原来的路径
			history.pushState():使用新的路径
			history.popState():路径的回退
			history.go():向前或者向后改变路径
			history.back():向后改变路径
			
```

![QQ截图20220607102052.png](https://img1.imgtp.com/2022/09/13/WPC9CvI7.png)

```
2.react-router有两部分组成
	1.react-router//这是核心代码
	2.react-router-dom//这是用于浏览器的
	3.react-router-native//这个适用于原生应用
	使用react-router需要使用npm install react-router-dom 他会自动安装react-router
	
3.react-router的组件使用
	<BrowserRouter>是使用history模式
	import * as React from "react";
    import * as ReactDOM from "react-dom";
    import { BrowserRouter } from "react-router-dom";
    ReactDOM.render(
      <BrowserRouter>
        <App/>
      </BrowserRouter>,
      root
    );//这就是目前比较普遍的用法，也是官方比较推荐的用法，将他包裹在App组件
   3.1<HashRouter>是使用hash模式，官方不推荐使用
   3.2<link>组件是跳转的组件，它最终会被渲染成a元素
   3.3<NavLink>是在Link的基础上增加了一些样式
   		3.3.1 to属性是Link和NavLink组件中最重要的属性，用于设置跳转的路径
   3.4.Route组件
   		3.4.1 Route组件主要适用于路由路径的匹配
   		3.4.2 path属性:用于设置匹配到的路径
   		3.4.3 component属性：设置匹配到的路径后渲染的组件，
   		3.4.4.exact:精准匹配
   		上述的属性属于v5的版本
   		在v6版本中所有<Route>组件将放在<Routes>中，component改成element,   
```

![QQ截图20220607144416.png](https://img1.imgtp.com/2022/09/13/TzYXcXQV.png)

![QQ截图20220607215036.png](https://img1.imgtp.com/2022/09/13/XwuhkKsl.png)

![QQ截图20220607144950.png](https://img1.imgtp.com/2022/09/13/djJ6Fots.png)

```
<NavLink>的用法
用于可以处于当前访问连接时修改连接的样式，处于active状态
v5的写法
可以添加如下属性
activeStyle:活跃时的样式
activeClassName：活跃时添加class
exact精确匹配
也可以使用自定义class
```

![QQ截图20220607151506.png](https://img1.imgtp.com/2022/09/13/Ml03VBOs.png)

v6的写法，不需要activeStyle和activeClassName，可以在普通的style和className中传入一个函数，更加灵活方便

![QQ截图20220607151735.png](https://img1.imgtp.com/2022/09/13/EfvZ4TfB.png)

```
Switch的作用 V5 在V6没有该组件
```

组件中获取路由参数，params中存在对饮的路由参数

```
const params = useParams()
```

#### Navigate导航

Navigate用于路由的重定向

```
<Route path='/home' element={<Navigate to="/home/recommend"/>}/>
```

#### Not Found页面配置

```
<Route path='*' element={<NotFound/>}/>
```

### Search Params

![QQ截图20220607203231.png](https://img1.imgtp.com/2022/09/13/TdiJP94D.png)

### useNavigate

```
import { useNavigate } from "react-router-dom";

function SignupForm() {
  let navigate = useNavigate();

  async function handleSubmit(event) {
    event.preventDefault();
    await submitForm(event.target);
    navigate("../success", { replace: true });
  }

  return <form onSubmit={handleSubmit}>{/* ... */}</form>;
}
使用useNavigate可以实现手动进行路由跳转，let navigate = useNavigate();他会返回一个函数，参数有两个，一个是to, 另一个optional second { replace, state } arg，或者使用历史调用栈类似于go(-1)调用navigate(-1)
```

### useLocation

```
  let location = useLocation();//获取location
```

![QQ截图20220607212846.png](https://img1.imgtp.com/2022/09/13/LD2qs0Cd.png)

### withRouter(component)

```js
import {
  useLocation,
  useNavigate,
  useParams,
  useSearchParams
} from "react-router-dom";

function withRouter(Component) {
  function ComponentWithRouterProp(props) {
    const location = useLocation();
    const navigate = useNavigate();
    const params = useParams();
    const [searchParams] = useSearchParams()
    const query = Object.fromEntries(searchParams)
    return (
      <Component
        {...props}
        router={{ location, navigate, params,query }}
      />
    );
  }

  return ComponentWithRouterProp;
}//在v6中没有这个高阶组件，因为v6都是使用到的是函数式组件，所以不需要，如果想用可以这样封装，在v5中的主要目的是为了手动调用路由可以获取到history,组件可以通过this.props.history,push()来手动进行路由跳转，v6可以使用到useNavigate()
```

#### 路由的配置文件

通过像vue-router来将路由定义在单独的文件中

```js
//router.js
import Home from '../pages/Home'
import HomeRecommend from "../pages/HomeRecommend"
import HomeRanking from "../pages/HomeRanking"
import HomeSongMenu from '../pages/HomeSongMenu'
import Category from "../pages/Category"
import Order from "../pages/Order"
import NotFound from '../pages/NotFound'
import Detail from '../pages/Detail'
import User from '../pages/User'
import { Navigate } from 'react-router-dom'
import React from 'react'
const About = React.lazy(() => import("../pages/About"))
const Login = React.lazy(() => import("../pages/Login"))
const routes = [
  {
    path: "/",
    element: <Navigate to="/home"/>
  },
  {
    path: "/home",
    element: <Home/>,
    children: [
      {
        path: "/home",
        element: <Navigate to="/home/recommend"/>
      },
      {
        path: "/home/recommend",
        element: <HomeRecommend/>
      },
      {
        path: "/home/ranking",
        element: <HomeRanking/>
      },
      {
        path: "/home/songmenu",
        element: <HomeSongMenu/>
      }
    ]
  },
  {
    path: "/about",
    element: <About/>
  },
  {
    path: "/login",
    element: <Login/>
  },
  {
    path: "/category",
    element: <Category/>
  },
  {
    path: "/order",
    element: <Order/>
  },
  {
    path: "/detail/:id",
    element: <Detail/>
  },
  {
    path: "/user",
    element: <User/>
  },
  {
    path: "*",
    element: <NotFound/>
  }
]
export default routes
```

```
 {useRoutes(routes)}
```

