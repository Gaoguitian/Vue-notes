# Vue-router

## 1.路由简介

**什么是路由？**

- 路由就是通过互联的网络把信息从源地址传送到目的地的活动
- 路由提供了两种机制：路由和传送
  - 路由是决定数据包从来源到目的地的路径
  - 转送就是将数据转移
- 路由表
  - 路由表本质就是一个映射表，决定了数据包的指向



## 2.URL的hash和HTML5的history

### 2.1URL的hash

- URL的hash是通过锚点(#)，其本质上改变的是window.location的href属性。
- 可以通过直接赋值location.hash来改变href，但是页面并不会发生刷新。

vue-router其实用的就是这样的机制，改变url地址，这个url地址存在一份路由映射表里面，比如`/user`代表要请求用户页面，只要配置了这个路由表（路由关系），就可以前端跳转而不刷新页面，所有的数据请求都走ajax。

### 2.2HTML5的history模式

#### 2.2.1 pushState

同样的使用HTML5的history模式也是不会刷新页面的,history对象栈结构，先进后出，pushState类似压入栈中，back是回退。

```js
hristory.pushState({}, '', '/foo')
history.back()
```

#### 2.2.2 replaceState

replaceState模式与pushState模式区别在于replaceState模式浏览器没有返回只是替换，不是压入栈中。

```JS
history.replaceState({}, '', 'home')
```

#### 2.2.3 go

go只能在pushState模式中使用，go是前进后退到哪个历史页面。

```JS
history.go(-1)//回退一个页面
history.go(1)//前进一个页面
history.forward()//等价于go(1)
history.back()//等价于go(-1)
```

## 3.vue-router的安装

### 直接下载 / CDN

```html
<script src="/path/to/vue.js"></script>
<script src="/path/to/vue-router.js"></script>
//在引入vue文件之后方可引入vue-router文件，因为vue-router是基于vue的
```

### NPM

```bash
npm install vue-router
```

如果在一个模块化工程中使用它，必须要通过 `Vue.use()` 明确地安装路由功能：

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)//使用vue-router
```

## 4.vue-router的基本使用

用 Vue.js + Vue Router 创建单页应用，核心就是将组件 (components) 映射到路由 (routes)，然后告诉 Vue Router 在哪里渲染它们。

### 4.1 添加路由链接router-link

```html
<div id="app">
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
</div>
```

#### 4.1.1router-link介绍

`<router-link>` 组件支持用户在具有路由功能的应用中 (点击) 导航。 通过 `to` 属性指定目标地址，默认渲染成带有正确链接的 `<a>` 标签，可以通过配置 `tag` 属性生成别的标签.。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的 CSS 类名。

#### 4.1.2router-link常用属性

#####   1.to

- 类型: `string | Location`
- required

表示目标路由的链接。当被点击后，内部会立刻把 `to` 的值传到 `router.push()`，所以这个值可以是一个字符串或者是描述目标位置的对象。

```html
<!-- 字符串 -->
<router-link to="home">Home</router-link>
<!-- 渲染结果 -->
<a href="home">Home</a>

<!-- 使用 v-bind 的 JS 表达式 -->
<router-link v-bind:to="'home'">Home</router-link>

<!-- 不写 v-bind 也可以，就像绑定别的属性一样 -->
<router-link :to="'home'">Home</router-link>

<!-- 同上 -->
<router-link :to="{ path: 'home' }">Home</router-link>

<!-- 命名的路由  一般用在动态路由当中,params表示动态路由传递的参数信息 -->  
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>

<!-- 带查询参数，下面的结果为 /register?plan=private -->
<router-link :to="{ path: 'register', query: { plan: 'private' }}"> Register </router-link>
```

##### 2.replace

- 类型: `boolean`
- 默认值: `false`

设置 `replace` 属性的话，当点击时，会调用 `router.replace()` 而不是 `router.push()`，于是导航后不会留下 history 记录。

原理就是使用了history.replace('')，直接替换

```html
<router-link :to="{ path: '/abc'}" replace></router-link>
```

##### 3.tag

- 类型: `string`
- 默认值: `"a"`

有时候想要 **`<router-link>` 渲染成某种标签**，例如 `<li>`。 于是我们使用 `tag` prop 类指定何种标签，同样它还是会监听点击，触发导航。

```html
<!-- 渲染成li标签-->
<router-link to="/foo" tag="li">foo</router-link>
<!-- 渲染结果  点击依旧可以触发路由跳转的功能-->
<li>foo</li>

<!-- 渲染成button标签-->
<router-link to="/foo" tag="button">foo</router-link>
<!-- 渲染结果  点击依旧可以触发路由跳转的功能-->
<button>foo</button>
```

##### 4.active-class

- 类型: `string`
- 默认值: `"router-link-active"`

设置链接激活时使用的 CSS 类名。默认值可以通过路由的构造选项 `linkActiveClass` 来全局配置。

```JS
new Router({
    //设置路由激活后链接的样式，active是类选择器，在css中设置active相应的属性
    linkActiveClass:'active'
})
```

### 4.2添加路由填充位router-view

```html
<router-view></router-view>
<!-- 路由填充位（也叫做路由占位符） -->
<!-- 将来通过路由规则匹配到的组件，将会被渲染到 router-view 所在的位置 --> 
<!-- 如果不写router-view标签将无法将组件内容渲染出来 --> 
```

将路由链接和路由填充位合并如下：

```html
<div id="app">
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
    <router-view></router-view>
</div>
```

当前可表示一个完整的路由html页面设置

#### 4.2.1router-view介绍

`<router-view>` 组件是一个 functional 组件，渲染路径匹配到的视图组件。`<router-view>` 渲染的组件还可以内嵌自己的 `<router-view>`，根据嵌套路径，渲染嵌套组件。

简单来说，就是将路由对应的组件在router-view闭合标签里面渲染出来

```html
<router-view>
  <component/> //需要展示的组件
</router-view>
```

#### 4.2.2 keep-alive介绍

##### 1.概念

keep-alive 是 Vue 的内置组件，当它包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 transition 相似，keep-alive 是一个抽象组件：它自身不会渲染成一个 DOM 元素，也不会出现在父组件链中。

##### 2.作用

在组件切换过程中将状态保留在内存中，防止重复渲染DOM，减少加载时间及性能消耗，提高用户体验性。而且组件不会频繁的被创建和销毁。

```html
<keep-alive>
    <router-views></router-views>
</keep-laive>
```

##### 3.Props

- include - 字符串或正则表达式。只有名称匹配的组件会被缓存。

- exclude - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。
- max - 数字。最多可以缓存多少组件实例。

```HTML
//1.将缓存 name 为 test 的组件
<keep-alive include='test'>
      <router-view/>
</keep-alive>

//2.将缓存 name 为 a 或者 b 的组件，结合动态组件使用
<keep-alive include='a,b'>
  	  <router-view/>
</keep-alive>
//3.将不缓存 name 为 test 的组件
<keep-alive exclude='test'>
  	  <router-view/>
</keep-alive>
```

##### 4.生命周期函数

1. activated

      在 keep-alive 组件激活时调用
      该钩子函数在服务器端渲染期间不被调用

  2. deactivated

      在 keep-alive 组件停用时调用
      该钩子在服务器端渲染期间不被调用

    被包含在 keep-alive 中创建的组件，会多出两个生命周期的钩子: activated 与 deactivated

### 4.3注册路由组件component

```js
var User = {
 template: '<div>User</div>'
 }
var Register = {
 template: '<div>Register</div>'
 }
```

### 4.4创建路由实例

在创建路由实例的步骤中需要对实例进行路由和组件的匹配

```js
const router = new VueRouter({routes:[]});
//引入vue-router文件后可通过VueRouter创建路由实例，通过routes可以设置路由规则
```

### 4.5设置路由规则routes

```js
const router = new VueRouter({routes:[
   	{
     path:'/设置路由信息，当前路由规则对应的hash地址/',
     component:'/设置路由组件，表示当前路由规则要展示的组件/'
    }
]});   

```
### 4.6挂载路由到根组件

```js
// 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')
```



### 4.7路由重定向

用处：当访问localhost:8080时，默认path是'  '或者' / '，此时没有配置他们的组件，没有任何的显示，而我们想要进入主页，这时候就需要路由的重定向了

路由重定向指的是：用户在访问地址 A 的时候，强制用户跳转到地址 C ，从而展示特定的组件页面； 通过路由规则的 redirect 属性，指定一个新的路由地址，可以很方便地设置路由的重定向：

```js
var router = new VueRouter({
 routes: [
 // 其中，path 表示需要被重定向的原地址，redirect 表示将要被重定向到的新地址
 {path:'/', redirect: '/user'},
 {path:'/user',component: User},
 {path:'/register',component: Register}
 ]
 })
```

##  5.vue-router嵌套路由

实际生活中的应用界面，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件，例如：

```text
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

实际应用界面，通常由多层嵌套的组件组合而成。
比如，我们 “首页”组件中，还嵌套着 “登录”和 “注册”组件，那么URL对应就是`/home/login`和`/home/reg`

借助 `vue-router`，使用嵌套路由配置，就可以很简单地表达这种关系。

###  5.1嵌套路由功能分析

1. 点击父级路由链接显示模板内容
2. 模板内容中又有子级路由链接
3. 点击子级路由链接显示子级模板内容

![image-20210618003309397](C:\Users\z1078\AppData\Roaming\Typora\typora-user-images\image-20210618003309397.png)

### 5.2父路由组件模板

1. 父级路由链接
2. 父组件路由填充位

```html
 <p>
    //父级路由的链接
 <router-link to="/user">User</router-link>
 <router-link to="/register">Register</router-link>
 </p>
 <div>
 <!-- 控制组件的显示位置 -->
 <router-view></router-view> 
 </div
```

### 5.3 子级路由模板

1. 子级路由链接
2. 子组件路由填充位

```HTML
 const Register = {
 template: `<div>
 <h1>Register 组件</h1>
 <hr/>
     //子级路由的链接
 <router-link to="/register/tab1">Tab1</router-link>
 <router-link to="/register/tab2">Tab2</router-link>
 <!-- 子路由填充位置 -->
 <router-view/>
 </div>`
 }

```

### 5.4嵌套路由配置*

使用children

要在嵌套的出口中渲染组件，需要在 `VueRouter` 的参数中使用 `children` 配置，在children里面配置子路由的内容，和定于路由规则相似都具有path、component等

```js
 const router = new VueRouter({
 routes: [
 { path: '/user', component: User },
 {
 path: '/register',
 component: Register,
 // 通过 children 属性，为 /register 添加子路由规则
 children: [
  /子路由path的两种写法，注意第二种/
 { path: '/register/tab1', component: Tab1 }, 
 { path: 'tab2', component: Tab2 }
       ]
     }
   ]
})

```

**但是还需要注意下面几点：**

**1.以 / 开头的嵌套路径会被当做根路径，** 这可以让你充分的使用嵌套组件而无需设置嵌套的路径。

2.注意需要匹配一个空的情况，然后匹配一个default组件，避免出现空白页的问题

**第2点需要特别注意，子路由是不能加"/"的，加了的话，就匹配不了了，而是匹配到根路由去了。**



## 6.vue-router动态路由匹配

用处：将同一模板的组件进行复用，但是他们可以有不同的参数

我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 `User` 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 `vue-router` 的路由路径中使用“动态路径参数”(dynamic segment) 来达到这个效果：

```js
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

现在呢，像 `/user/foo` 和 `/user/bar` 都将映射到相同的路由。

一个“路径参数”使用冒号 `:` 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。于是，我们可以更新 `User` 的模板，输出当前用户的 ID：

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```

你可以在一个路由中设置多段“路径参数”，对应的值都会设置到 `$route.params` 中。例如：

![image-20210618004550608](C:\Users\z1078\AppData\Roaming\Typora\typora-user-images\image-20210618004550608.png)

除了 `$route.params` 外，`$route` 对象还提供了其它有用的信息，例如，`$route.query` (如果 URL 中有查询参数)、`$route.hash` 等等。

### 6.1响应路由参数的变化

应用场景：当路由发生变化时做出相应的响应，比如说根据路由决定组件里面的信息。

提醒一下，当使用路由参数时，例如从 `/user/foo` 导航到 `/user/bar`，**原来的组件实例会被复用**。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。**不过，这也意味着组件的生命周期钩子不会再被调用**。

复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch (监测变化) `$route` 对象：

```js
const User = {
  template: '...',
  watch: {
    $route(to, from) {  //(new,old) to表示新路由地址，from表示旧路由地址
      // 对路由变化作出响应...
    }
  }
}
```

或者使用 2.2 中引入的 `beforeRouteUpdate` [导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)：

```js
const User = {
  template: '...',
  beforeRouteUpdate(to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

## 7.路由组件传参

### 7.1布尔模式

如果 `props` 被设置为 `true`，`route.params` 将会被设置为组件属性

在组件中使用 `$route` 会使之与其对应路由形成高度耦合，从而使组件只能在某些特定的 URL 上使用，限制了其灵活性。

使用 `props` 将组件和路由解耦：

**取代与 `$route` 的耦合**

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [{ path: '/user/:id', component: User }]
})
```

**通过 `props` 解耦**

```js
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```

### 7.2对象模式

如果 `props` 是一个对象，它会被按原样设置为组件属性。当 `props` 是静态的时候有用

```js
 const router = new VueRouter({
 routes: [
 // 如果 props 是一个对象，它会被按原样设置为组件属性
 { path: '/user/:id', component: User, props: { uname: 'lisi', age: 12 }}
 ]
 })
const User = {
 props: ['uname', 'age'],
 template: ‘<div>用户信息：{{ uname + '---' + age}}</div>'
 }
```

### 7.3函数模式

你可以创建一个函数返回 `props`。这样你便可以将参数转换成另一种类型，将静态值与基于路由的值结合等等。

```js
const router = new VueRouter({
 routes: [
 // 如果 props 是一个函数，则这个函数接收 route 对象为自己的形参
 { path: '/user/:id', 
 component: User, 
 props: route => ({ uname: 'zs', age: 20, id: route.params.id })}
 ]
 })
const User = {
 props: ['uname', 'age', 'id'],
 template: ‘<div>用户信息：{{ uname + '---' + age + '---' + id}}</div>'
 }

```



## 8.vue-router命名路由

为了更加方便的表示路由的路径，可以给路由规则起一个别名，即为“命名路由”。

主要是针对于动态路由的实现

```js
const router = new VueRouter({
 routes: [
 {
 path: '/user/:id',
 name: 'user',
 component: User
 }
 ]
 })
```

```html
 <router-link :to="{ name: 'user', params: { id: 123 }}">User</router-link>
//动态设置路由链接的参数
```

```js
 this.$router.push({ name: 'user', params: { id: 123 }}
//手动设置路由的跳转
```

## 9.vue-router编程式导航

### 9.1页面导航的两种方式

1. 声明式导航：通过点击链接实现导航的方式，叫做声明式导航

   例如：普通网页中的<a> </a>a 链接 或 vue 中的  router-link

2. 编程式导航:通过调用JavaScript形式的API实现导航的方式，叫做编程式导航

   例如：普通网页中的 location.href

### 9.2编程式导航基本用法

常用的编程式导航 API 如下： 

1. this.$router.push('hash地址') 
2.  this.$router.go(n)

```js
const User = {
 template: '<div><button @click="goRegister">跳转到注册页面</button></div>',
 methods: {
 goRegister: function(){
 // 用编程的方式控制路由跳转
 this.$router.push('/register');
 }
 }
 }
```

### 9.3编程式导航参数规则

router.push() 方法的参数规则

```js
 // 字符串(路径名称)
 router.push('/home')
 // 对象
 router.push({ path: '/home' })
 // 命名的路由(传递参数)
 router.push({ name: '/user', params: { userId: 123 }})
 // 带查询参数，变成 /register?uname=lisi
 router.push({ path: '/register', query: { uname: 'lisi' }})
```



## 10.HTML5 History 模式

`vue-router` 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

通过使用hash的话地址栏会有#'  '的情况出现

如果不想要很丑的 hash，我们可以用路由的 **history 模式**，这种模式充分利用 `history.pushState` API 来完成 URL 跳转而无须重新加载页面。

```js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
}
```

当你使用 history 模式时，URL 就像正常的 url，例如 `http://yoursite.com/user/id`，也好看！

## 11.路由懒加载

当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。

结合 Vue 的[异步组件 (opens new window)](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#异步组件)和 Webpack 的[代码分割功能 (opens new window)](https://doc.webpack-china.org/guides/code-splitting-async/#require-ensure-/)，轻松实现路由组件的懒加载。

首先，可以将异步组件定义为返回一个 Promise 的工厂函数 (该函数返回的 Promise 应该 resolve 组件本身)：

```js
const Foo = () =>
  Promise.resolve({
    /* 组件定义对象 */
  })
```

第二，在 Webpack 2 中，我们可以使用[动态 import (opens new window)](https://github.com/tc39/proposal-dynamic-import)语法来定义代码分块点 (split point)：

```js
import('./Foo.vue') // 返回 Promise
```

结合这两者，这就是如何定义一个能够被 Webpack 自动代码分割的异步组件。

```js
const Foo = () => import('./Foo.vue')
```

在路由配置中什么都不需要改变，只需要像往常一样使用 `Foo`：

```js
const router = new VueRouter({
  routes: [{ path: '/foo', component: Foo }]
})
```

## 12.导航守卫

### 12.1 全局前置守卫

你可以使用 `router.beforeEach` 注册一个全局前置守卫：

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 **等待中**。

每个守卫方法接收三个参数：

- **`to: Route`**: 即将要进入的目标 [路由对象](https://router.vuejs.org/zh/api/#路由对象)
- **`from: Route`**: 当前导航正要离开的路由
- **`next: Function`**: 一定要调用该方法来 **resolve** 这个钩子。执行效果依赖 `next` 方法的调用参数。
  - **`next()`**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
  - **`next(false)`**: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
  - **`next('/')` 或者 `next({ path: '/' })`**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 [`router-link` 的 `to` prop](https://router.vuejs.org/zh/api/#to) 或 [`router.push`](https://router.vuejs.org/zh/api/#router-push) 中的选项。
  - **`next(error)`**: (2.4.0+) 如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 [`router.onError()`](https://router.vuejs.org/zh/api/#router-onerror) 注册过的回调。

**确保 `next` 函数在任何给定的导航守卫中都被严格调用一次。它可以出现多于一次，但是只能在所有的逻辑路径都不重叠的情况下，否则钩子永远都不会被解析或报错**。这里有一个在用户未能验证身份时重定向到 `/login` 的示例：

类似于Node的中间件，拦截路由操作

```js
// BAD
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  // 如果用户未能验证身份，则 `next` 会被调用两次
  next()
})
```

```js
// GOOD
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})
```

### 12.2路由独享的守卫

你可以在路由配置上直接定义 `beforeEnter` 守卫：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

这些守卫与全局前置守卫的方法参数是一样的

### 12.3组件内的守卫***

最后，你可以在路由组件内直接定义以下路由导航守卫：

- `beforeRouteEnter`
- `beforeRouteUpdate` (2.2 新增)
- `beforeRouteLeave`

```js
const Foo = {
  template: `...`,
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

`beforeRouteEnter` 守卫 **不能** 访问 `this`，因为守卫在导航确认前被调用，因此即将登场的新组件还没被创建。

不过，你可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

注意 `beforeRouteEnter` 是支持给 `next` 传递回调的唯一守卫。对于 `beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以**不支持**传递回调，因为没有必要了。

```js
beforeRouteUpdate (to, from, next) {
  // just use `this`
  this.name = to.params.name
  next()
}
```

这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消。

```js
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```
