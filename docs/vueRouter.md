## Vue Router 
* [Vue Router1111 官网](https://router.vuejs.org/zh/introduction.html)

**Vue-Router导航守卫是什么** 

* 单页应用中，控制组件跳转、显示、控制的管理者。比如最常见的登录权限验证，当用户满足条件时，才让其进入导航，否则就取消跳转，并跳到登录页面让其登录。
* 建立起url和页面组件的映射关系。
* 组件控制器。

## 不同的历史模式
**`Hash` 模式**

`hash` 模式是用 `createWebHashHistory()`` 创建的：
```javascript
import { createRouter, createWebHashHistory } from 'vue-router'

const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    //...
  ],
})

```
它在内部传递的实际` URL `之前使用了一个哈希字符（`#`）。由于这部分 URL 从未被发送到服务器，所以它不需要在服务器层面上进行任何特殊处理。不过，它在` SEO（搜索引擎优化） `中确实有不好的影响。如果你担心这个问题，可以使用` HTML5 `模式。

**`HTML5` 模式**
用` createWebHistory() `创建 `HTML5` 模式，推荐使用这个模式：
```javascript
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    //...
  ],
})
```
当使用这种历史模式时，URL 会看起来很 "正常"，例如 https://example.com/user/id。

# HTML 标签
```html
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!--使用 router-link 组件进行导航 -->
    <!--通过传递 `to` 来指定链接 -->
    <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
    <router-link to="/">Go to Home</router-link>
    <router-link to="/about">Go to About</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```
## router-link
`<router-link to="/">Home</router-link>`

一个自定义组件` router-link `来创建链接。这使得` Vue Router `可以在不重新加载页面的情况下更改` URL`，处理` URL`的生成以及编码。我们将在后面看到如何从这些功能中获益。

## router-view
`<router-view></router-view>`

`router-view `将显示与` url `对应的组件。你可以把它放在任何地方，以适应你的布局。

```javascript
// 1. 定义路由组件.
// 也可以从其他文件导入
const Home = { template: '<div>Home</div>' }
const About = { template: '<div>About</div>' }

// 2. 定义一些路由
// 每个路由都需要映射到一个组件。
// 我们后面再讨论嵌套路由。
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
]

// 3. 创建路由实例并传递 `routes` 配置
// 你可以在这里输入更多的配置，但我们在这里
// 暂时保持简单
const router = VueRouter.createRouter({
  // 4. 内部提供了 history 模式的实现。为了简单起见，我们在这里使用 hash 模式。
  history: VueRouter.createWebHashHistory(),
  routes, // `routes: routes` 的缩写
})

// 5. 创建并挂载根实例
const app = Vue.createApp({})
//确保 _use_ 路由实例使
//整个应用支持路由。
app.use(router)

app.mount('#app')

// 现在，应用已经启动了！
```



## 全局守卫

**`vue router`全局有三个守卫**

1. **`router.beforeEach` 全局前置守卫（进入路由之前）**

```javascript
const router = createRouter({ ... })
router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消导航
  return false
})
```
```javascript
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})
```
2. **`router.beforeResolve` 全局解析守卫（进入路由之前）**
   * ` router.beforeResolve `是获取数据或执行任何其他操作（如果用户无法进入页面时你希望避免执行的操作）的理想位置。

3. **`router.afterEach` 全局后置钩子 （进入路由之后）**
    * 分析、更改页面标题、声明页面等辅助功能

**每个守卫方法接收两个参数**
to和from是**将要进入**和**将要离开**的路由对象,路由对象指的是平时通过this.$route获取到的路由对象。
* `to`：即将要进入的目标；
* `from`：当前导航正要离开的路由； 
* 可选的第三个参数 `next`， 确保 `next` 在任何给定的导航守卫中都被严格调用一次。

可以返回的值如下：`undefined` 或返回 `true`则导航是有效的，并调用下一个导航守卫， `return false`取消当前的导航。

---
## 路由独享的守卫
**你可以直接在路由配置上定义 `beforeEnter` 守卫**
```javascript
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
**`beforeEnter` 守卫 只在进入路由时触发，不会在 `params`、`query` 或 `hash` 改变时触发。例如，从 `/users/2` 进入到 `/users/3` 或者从 `/users/2#info` 进入到 `/users/2#projects`。它们只有在 从一个不同的 路由导航时，才会被触发。**

## 组件内的守卫
```javascript
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

## `vue router` 生命周期
* 导航被触发
* 在失活的组件里调用 beforeRouteLeave 守卫。
* 调用全局的 beforeEach 守卫。
* 在重用的组件里调用 beforeRouteUpdate 守卫(2.2+)。
* 在路由配置里调用 beforeEnter。
* 解析异步路由组件。
* 在被激活的组件里调用 beforeRouteEnter。
* 调用全局的 beforeResolve 守卫(2.5+)。
* 导航被确认。
* 调用全局的 afterEach 钩子。
* 触发 DOM 更新。
* 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## 路由元信息
有时，你可能希望将任意信息附加到路由上，如过渡名称、谁可以访问路由等。这些事情可以通过接收属性对象的 ` meta `属性来实现，并且它可以在路由地址和导航守卫上都被访问到。定义路由的时候你可以这样配置` meta `字段：

```javascript
const routes = [
  {
    path: '/posts',
    component: PostsLayout,
    children: [
      {
        path: 'new',
        component: PostsNew,
        // 只有经过身份验证的用户才能创建帖子
        meta: { requiresAuth: true }
      },
      {
        path: ':id',
        component: PostsDetail
        // 任何人都可以阅读文章
        meta: { requiresAuth: false }
      }
    ]
  }
]

```

如何访问这个` meta `字段呢？

首先，我们称呼` routes `配置中的每个路由对象为**路由记录**。路由记录可以是嵌套的，因此，当一个路由匹配成功后，它可能匹配多个路由记录。

例如，根据上面的路由配置，`/posts/new` 这个` URL `将会匹配父路由记录` (path: '/posts') `以及子路由记录` (path: 'new')`。

```javascript
import { useRouter, useRoute } from 'vue-router'
const router = useRouter()
const route = useRoute()
 //通过route来获取路由元信息
 console.log(route.meta);
```

## [后台项目——权限路由](https://juejin.cn/post/6932744687660990477)

## [Fantastic-admin 官方文档](https://hooray.gitee.io/fantastic-admin/)