---
title: vue-router笔记
date: 2022-08-20 16:29:09
tags:
categories:
- [vue, note]
---

#### 路由器维护一张映射表，映射表会决定数据的流向，一个ip地址对应的一台服务器的mac地址，一台服务器只有一个mac地址

#### url的hash

url的hash也就是锚点(#),本质上就是改变window.location的href的属性

我们可以通过赋值location.hash来改变href,但是不刷新页面

hash的优势就是兼容性好，在IE中可以运行，但是就是有个缺陷，#，显得不像是一个真实的路径

```js
<body>
    <div id="app">
      <a href="#/home">home</a>
      <a href="#/about">about</a>
    </div>
    <script>
      const app = document.querySelector("#app");
      window.addEventListener("hashchange", () => {
        console.log(location.hash);
        switch (location.hash) {
          case "#/home":
            app.innerHTML = "home";
            break;
          case "#/about":
            app.innerHTML = "about";
            break;
          default:
            app.innerHTML = "app";
        }
      });
    </script>
  </body>
```

#### html5 history api

history是html5新增的api 他有六种模式改变url而不刷新页面

replaceState  替换原来的路径

pushState  使用新的路径

popState 路径回退

go 向前或者向后改变路径

forward 向前改变路径

back 向后改变路径

```js
  <body>
    <div id="App">
      <a href="/about">about</a>
      <a href="/home">home</a>
      <div class="show"></div>
      <script>
        const show = document.getElementsByClassName("show")[0];
        const elea = document.getElementsByTagName("a");
        console.log(elea);
        window.addEventListener("popstate", historyChange);
        window.addEventListener("go", historyChange);
        for (const item of elea) {
          item.addEventListener("click", (e) => {
            e.preventDefault();
            const href = item.getAttribute("href");
            console.log(href);
            history.pushState({}, "", href);
            historyChange();
          });
        }

        function historyChange() {
          switch (location.pathname) {
            case "/about":
              show.innerHTML = "about";
              break;
            case "/home":
              show.innerHTML = "home";
              break;
            default:
              break;
          }
        }
      </script>
    </div>
  </body>
```

#### 安装

```
npm install vue-router
```

##### 使用步骤

router/index.js

```js
import {
  createRouter,
  createWebHashHistory,
  createWebHistory,
} from "vue-router";

// import Home from '../Views/Home.vue'
// import About from '../Views/About.vue'

// 路由的懒加载 ，可以进行分包，减少报的大小，进行按需加载   webpackChunkName设置包的名字
// const Home = () => import(/* webpackChunkName: 'home' */"../Views/Home.vue")
// const About = () => import(/* webpackChunkName: 'about' */"../Views/About.vue")

// 创建一个路由: 映射关系
const router = createRouter({
  // 指定采用的模式: hash
  history: createWebHashHistory(),
  // history: createWebHistory(),
  // 映射关系
  routes: [
    {//设置默认路径并进行重定向
      path: "/",
      redirect: "/home",
    },
    {
      name: "home",//路由记录独一无二的名称
      path: "/home",
      component: () => import("../Views/Home.vue"),//组件懒加载
      meta: {//这是路由元信息，不在url中携带的信息
        name: "why",
        age: 18,
      },
       //子路由
      children: [
        {
          path: "/home",
          redirect: "/home/recommend",
        },
        {
          path: "recommend", // /home/recommend
          component: () => import("../Views/HomeRecommend.vue"),
        },
        {
          path: "ranking", // /home/ranking
          component: () => import("../Views/HomeRanking.vue"),
        },
      ],
    },
    {
      name: "about",
      path: "/about",
      component: () => import("../Views/About.vue"),
    },
    {
      path: "/user/:id",
      component: () => import("../Views/User.vue"),
    },
    {
      path: "/order",
      component: () => import("../Views/Order.vue"),
    },
    {
      path: "/login",
      component: () => import("../Views/Login.vue"),
    },
    {
      // abc/cba/nba  配置404页面
      path: "/:pathMatch(.*)*",
      component: () => import("../Views/NotFound.vue"),
    },
  ],
});

// 1.动态管理路由
let isAdmin = true;
if (isAdmin) {
  // 一级路由
  router.addRoute({
    path: "/admin",
    component: () => import("../Views/Admin.vue"),
  });

  // 添加vip页面 "home"必须唯一
  router.addRoute("home", {
    path: "vip",
    component: () => import("../Views/HomeVip.vue"),
  });
}

// 获取router中所有的映射路由对象
console.log(router.getRoutes());

// 2.路由导航守卫
// 进行任何的路由跳转之前, 传入的beforeEach中的函数都会被回调
// 需求: 进入到订单(order)页面时, 判断用户是否登录(isLogin -> localStorage保存token)
// 情况一: 用户没有登录, 那么跳转到登录页面, 进行登录的操作
// 情况二: 用户已经登录, 那么直接进入到订单页面
router.beforeEach((to, from) => {
  // 1.进入到任何别的页面时, 都跳转到login页面
  // if (to.path !== "/login") {
  //   return "/login"
  // }

  // 2.进入到订单页面时, 判断用户是否登录
  const token = localStorage.getItem("token");
  if (to.path === "/order" && !token) {
    return "/login";
  }
});

export default router;
```

//main.js

```
// 5. 创建并挂载根实例
const app = Vue.createApp(App)
//确保 _use_ 路由实例使
//整个应用支持路由。注册插件
app.use(router)

app.mount('#app')
```

在组建中使用

```
<router-link>和<router-view>
//home.vue
<div class="home-nav">
      <router-link to="/home/recommend">推荐</router-link>
      <router-link to="/home/ranking">排行</router-link>
</div>

    <button @click="logoutClick">退出登录</button>

    <!-- 占位组件 -->
   <router-view></router-view>
```

## `<router-link>` Props

表示目标路由的链接。当被点击后，内部会立刻把 `to` 的值传到 `router.push()`，所以这个值可以是一个 `string` 或者是[描述目标位置的对象](https://router.vuejs.org/zh/api/#routelocationraw)。

```html
<!-- 字符串 -->
<router-link to="/home">Home</router-link>
<!-- 渲染结果 -->
<a href="/home">Home</a>

<!-- 使用 v-bind 的 JS 表达式 -->
<router-link :to="'/home'">Home</router-link>

<!-- 同上 -->
<router-link :to="{ path: '/home' }">Home</router-link>

<!-- 命名的路由 -->
<router-link :to="{ name: 'user', params: { userId: '123' }}">User</router-link>

<!-- 带查询参数，下面的结果为 `/register?plan=private` -->
<router-link :to="{ path: '/register', query: { plan: 'private' }}">
  Register
</router-link>
```

##### replace

设置 `replace` 属性的话，当点击时，会调用 `router.replace()`，而不是 `router.push()`，所以导航后不会留下历史记录。

```html
<router-link to="/abc" replace></router-link>
```

##### active-class

链接激活时，应用于渲染的 `<a>` 的 class

**默认值**：`"router-link-active"`

```
 <router-link to="/about" replace active-class="active">关于</router-link>
```

可以修改默认的class或者添加新的class

##### exact-active-class

链接精准激活时，应用于渲染的 `<a>` 的 class

**默认值**：`"router-link-exact-active"`

#### 带参数的动态路由匹配

```js
const User = {
  template: '<div>User</div>',
}

// 这些都会传递给 `createRouter`
const routes = [
  // 动态字段以冒号开始
  { path: '/users/:id', component: User },
]
```

参数可以被当前路由的params获取

```
const User = {
  template: '<div>User {{ $route.params.id }}</div>',
}
```

##### 监听响应路由参数的变化

使用带有参数的路由时需要注意的是，当用户从 `/users/johnny` 导航到 `/users/jolyne` 时，**相同的组件实例将被重复使用**。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。**不过，这也意味着组件的生命周期钩子不会被调用**。

要对同一个组件中参数的变化做出响应的话，你可以简单地 watch `$route` 对象上的任意属性，在这个场景中，就是 `$route.params` ：

```js
const User = {
  template: '...',
  created() {
    this.$watch(
      () => this.$route.params,
      (toParams, previousParams) => {
        // 对路由变化做出响应...
      }
    )
  },
}
```

或者，使用 `beforeRouteUpdate` [导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)，它也可以取消导航：

```
const User = {
  template: '...',
  async beforeRouteUpdate(to, from) {
    // 对路由变化做出响应...
    this.userData = await fetchUser(to.params.id)
  },
}
```

##### **捕获所有路由或 404 Not found 路由**

常规参数只匹配 url 片段之间的字符，用 `/` 分隔。如果我们想匹配**任意路径**，我们可以使用自定义的 *路径参数* 正则表达式，在 *路径参数* 后面的括号中加入 正则表达式 :

```js
const routes = [
  // 将匹配所有内容并将其放在 `$route.params.pathMatch` 下
  { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
  // 将匹配以 `/user-` 开头的所有内容，并将其放在 `$route.params.afterUser` 下
  { path: '/user-:afterUser(.*)', component: UserGeneric },
]
```

在这个特定的场景中，我们在括号之间使用了[自定义正则表达式](https://router.vuejs.org/zh/guide/essentials/route-matching-syntax.html#在参数中自定义正则)，并将`pathMatch` 参数标记为[可选可重复](https://router.vuejs.org/zh/guide/essentials/route-matching-syntax.html#可选参数)。这样做是为了让我们在需要的时候，可以通过将 `path` 拆分成一个数组，直接导航到路由：

```
this.$router.push({
  name: 'NotFound',
  // 保留当前路径并删除第一个字符，以避免目标 URL 以 `//` 开头。
  params: { pathMatch: this.$route.path.substring(1).split('/') },
  // 保留现有的查询和 hash 值，如果有的话
  query: this.$route.query,
  hash: this.$route.hash,
})
```

```
 <h2>NotFound: 您当前的路径{{ $route.params.pathMatch }}不正确, 请输入正确的路径!</h2>
```

这里还有另外一种写法：  注意：我在/:pathMatch(.*)后面又加了一个 *；

这时候$route.params.pathMatch将是数组

#### 编程式导航

**在 Vue 实例中，你可以通过 `$router` 访问路由实例。因此你可以调用`this.$router.push`**

想要导航到不同的 URL，可以使用 `router.push` 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，会回到之前的 URL

当你点击 `<router-link>` 时，内部会调用这个方法，所以点击 `<router-link :to="...">` 相当于调用 `router.push(...)`

```js
// 字符串路径
router.push('/users/eduardo')

// 带有路径的对象
router.push({ path: '/users/eduardo' })

// 命名的路由，并加上参数，让路由建立 url
router.push({ name: 'user', params: { username: 'eduardo' } })

// 带查询参数，结果是 /register?plan=private
router.push({ path: '/register', query: { plan: 'private' } })

// 带 hash，结果是 /about#team
router.push({ path: '/about', hash: '#team' })
```

**注意**：如果提供了 `path`，`params` 会被忽略，上述例子中的 `query` 并不属于这种情况。取而代之的是下面例子的做法，你需要提供路由的 `name` 或手写完整的带有参数的 `path` ：

```js
const username = 'eduardo'
// 我们可以手动建立 url，但我们必须自己处理编码
router.push(`/user/${username}`) // -> /user/eduardo
// 同样
router.push({ path: `/user/${username}` }) // -> /user/eduardo
// 如果可能的话，使用 `name` 和 `params` 从自动 URL 编码中获益
router.push({ name: 'user', params: { username } }) // -> /user/eduardo
// `params` 不能与 `path` 一起使用
router.push({ path: '/user', params: { username } }) // -> /user
```

当指定 `params` 时，可提供 `string` 或 `number` 参数（或者对于[可重复的参数](https://router.vuejs.org/zh/guide/essentials/route-matching-syntax.html#repeatable-params)可提供一个数组）。**任何其他类型（如 `undefined`、`false` 等）都将被自动字符串化**。对于[可选参数](https://router.vuejs.org/zh/guide/essentials/route-matching-syntax.html#repeatable-params)，你可以提供一个空字符串（`""`）来跳过它。

由于属性 `to` 与 `router.push` 接受的对象种类相同，所以两者的规则完全相同。

`router.push` 和所有其他导航方法都会返回一个 *Promise*，让我们可以等到导航完成后才知道是成功还是失败。我们将在 [Navigation Handling](https://router.vuejs.org/zh/guide/advanced/navigation-failures.html) 中详细介绍。

##### router.replace

它在导航时不会向 history 添加新记录

```
router.push({ path: '/home', replace: true })
// 相当于
router.replace({ path: '/home' })
```

```
// 向前移动一条记录，与 router.forward() 相同
router.go(1)

// 返回一条记录，与 router.back() 相同
router.go(-1)

// 前进 3 条记录
router.go(3)

// 如果没有那么多记录，静默失败
router.go(-100)
router.go(100)
```

#### 命名视图

用于相同的path之间的切换组件，想同时 (同级) 展示多个视图

你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 `router-view` 没有设置名字，那么默认为 `default`

```
<router-view class="view left-sidebar" name="LeftSidebar"></router-view>
<router-view class="view main-content"></router-view>
<router-view class="view right-sidebar" name="RightSidebar"></router-view>
```

```
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/',
      components: {
        default: Home,
        // LeftSidebar: LeftSidebar 的缩写
        LeftSidebar,
        // 它们与 `<router-view>` 上的 `name` 属性匹配
        RightSidebar,
      },
    },
  ],
})
```

#### 导航守卫

##### 全局前置守卫

```js
const router = createRouter({ ... })

router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消导航
  return false
})
```

##### 路由独享的守卫

```js
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
]
```

##### 组件内的守卫

```
const UserDetails = {
  template: `...`,
  beforeRouteEnter(to, from) {
    // 在渲染该组件的对应路由被验证前调用
    // 不能获取组件实例 `this` ！
    // 因为当守卫执行时，组件实例还没被创建！
  },
  beforeRouteUpdate(to, from) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 `/users/:id`，在 `/users/1` 和 `/users/2` 之间跳转的时候，
    // 由于会渲染同样的 `UserDetails` 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 因为在这种情况发生的时候，组件已经挂载好了，导航守卫可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from) {
    // 在导航离开渲染该组件的对应路由时调用
    // 与 `beforeRouteUpdate` 一样，它可以访问组件实例 `this`
  },
}
```

### 使用组合 API[#](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#使用组合-api)

如果你正在使用[组合 API 和 `setup` 函数](https://v3.vuejs.org/guide/composition-api-setup.html#setup)来编写组件，你可以通过 `onBeforeRouteUpdate` 和 `onBeforeRouteLeave` 分别添加 update 和 leave 守卫

## 完整的导航解析流程[#](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#完整的导航解析流程)

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫(2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫(2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。

#### Vue Router 和 组合式 API

```
import { useRouter, useRoute } from 'vue-router'

export default {
  setup() {
    const router = useRouter()
    const route = useRoute()

    function pushWithQuery(query) {
      router.push({
        name: 'search',
        query: {
          ...route.query,
        },
      })
    }
  },
}
```

//useRouter=>$router  useRoute=>$route

##### `useLink`

#### 动态路由

动态路由主要通过两个函数实现。`router.addRoute()` 和 `router.removeRoute()`。它们**只**注册一个新的路由，也就是说，如果新增加的路由与当前位置相匹配，就需要你用 `router.push()` 或 `router.replace()` 来**手动导航**，才能显示该新路由

##### 添加嵌套路由

要将嵌套路由添加到现有的路由中，可以将路由的 *name* 作为第一个参数传递给 `router.addRoute()`，这将有效地添加路由，就像通过 `children` 添加的一样：

```
router.addRoute({ name: 'admin', path: '/admin', component: Admin })
router.addRoute('admin', { path: 'settings', component: AdminSettings }
```

这等效于：

```
router.addRoute({
  name: 'admin',
  path: '/admin',
  component: Admin,
  children: [{ path: 'settings', component: AdminSettings }],
})
```

Vue Router 提供了两个功能来查看现有的路由：

- [`router.hasRoute()`](https://router.vuejs.org/zh/api/#hasroute)：检查路由是否存在。
- [`router.getRoutes()`](https://router.vuejs.org/zh/api/#getroutes)：获取一个包含所有路由记录的数组
