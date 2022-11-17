## 路由基础

### 概念

路由（Router）就是对应关系，即 Hash 地址（锚链接）与组件之间的对应关系

与在 h5 中使用锚链接定位类似

```
<a href="#id1"></a>

<div id="id1"></div?
```

Hash 地址：url地址中 `#`后面的地址

### 前端路由的工作方式

1. 用户点击页面上的路由链接
2. URL地址中的 Hash 地址发生变化（#xxx）
3. 前端路由监听 Hash 地址变化
4. 前端路由把当前 Hash 地址对应的组件渲染出来

![image-20221007110311270](vue5.assets/image-20221007110311270.png)

window.onhashchange = ()=>{} 可以用于监听url 的 hash 地址变化，location.hash 可以获得当前页面的 hash 地址

### vue-router

vue-router 是 vue 官方给出的路由解决方案，用于管理 SPA 项目中组件的切换

官网：

> https://router.vuejs.org/zh/

#### 使用

1. 安装 vue-router

   ```
   // Vue2 项目中的安装命令
   npm i vue-router@3.5.2 -S
   ```

2. 创建路由模块：在 src 源代码目录下，新建 router/index.js 路由模块，并进行初始化

   ```js
   // 导入
   import Vue from 'vue'
   import VueRouter from 'vue-router'
   
   // 调用 Vue.use() 函数 把VueRouter安装为 vue 的插件
   Vue.use(VueRouter)
   
   // 创建路由的实例对象
   const router = new VueRouter()
   
   // 向外共享路由的实例对象
   export default router
   ```

3. 导入并挂载路由模块（在 `main.js`中导入）

   ```js
   import Vue from 'vue'
   import App from './App.vue'
   // 导入路由模块 拿到路由的实例对象
   import router from '@/router/index.js'
   // 也可以使用 import router from '@/router'
   // 模块化导入的时候 如果只指定了文件夹 则会自动寻找 index.xx
   
   Vue.config.productionTip = false
   
   new Vue({
     render: h => h(App),
     // 为了使用路由 需要把路由的实例对象挂载到 router
     // 由于属性和值同名 也可以直接简写为 router
     router: router
     
   }).$mount('#app')
   
   ```

   注意这里的 index.js 在使用 vue cli 去创建的时候，如果勾选了路由，则会自动生成 `index.js` 文件并挂载

4. 声明路由链接和占位符

   1. 在 vue 文件中定义链接以及占位符（router-view是由 vue-router 提供的标签）

      ```vue
      <!-- 链接 -->
      <a href="#/home">Home</a>
      <a href="#/movie">Movie</a>
      <a href="#/about">About</a>
      
      <!-- router-view 标签由 vue-router 提供 其作用相当于占位符 -->
      <router-view></router-view>
      ```

   2. 在 `index.js` 文件中指定 hash 地址与组件的对应关系（**路由规则**）

      ```js
      // 导入要使用的组件
      import Home from '@/components/Home.vue'
      import Movie from '@/components/Movie.vue'
      import About from '@/components/About.vue'
      
      const router = new VueRouter({
        // routes 数组用于指定 hash 地址与组件的对应关系 - 路由规则
        routes:[
          {path: '/home', component: Home},
          {path: '/movie', component: Movie},
          {path: '/about', component: About},
        ]
      })
      ```

      **注意这里 routes 数组中对 path 做了简化，无需写`#`**

#### 使用 router-link 替换链接

vue-router 提供了表示链接的标签 `router-link`，使用格式为：

```vue
<router-link to="/home">Home</router-link>
<router-link to="/movie">Movie</router-link>
<router-link to="/about">About</router-link>
```

注意这里也简化了地址的写法，不用添加`#`，该标签功能与 `<a>`标签一样，检查元素会发现实际上也是将 router-link 替换为 a 标签

#### 路由重定向

路由重定向是指当访问 a 地址时，强制用户跳转到地址 c，该功能通过 redirect 属性实现：

```js
routes:[
    // 将 #/ 强制转到 #/home
    {path: '/', redirect: '/home'},
    {path: '/home', component: Home},
    {path: '/movie', component: Movie},
    {path: '/about', component: About},
]
```

路由重定向可以防止用户直接访问根页面的时候看到的是空页面

##  嵌套路由

嵌套路由是指通过路由实现组件的嵌套展示，即模板内容中也有子级的路由链接，点击子级路由链接显示子级模板内容

嵌套路由与普通路由使用类似，首先在 vue 文件中定义路由链接和路由占位符：（**这里要注意链接地址要写从根地址开始的地址 /about/tab1**）

```vue
<template>
  <div class="about-container">
    <h3>About 组件</h3>
    <!-- 子级路由链接 -->
    <router-link to="/about/tab1">tab1</router-link>
    <router-link to="/about/tab2">tab2</router-link>
    <hr />

    <!-- 子级路由占位符 -->
    <router-view></router-view>
  </div>
</template>
```

随后在 `index.js`文件中导入要使用的组件：

```js
import Tab1 from '@/components/tabs/Tab1.vue'
import Tab2 from '@/components/tabs/Tab2.vue'
```

最后声明子路由规则：

```js
const router = new VueRouter({
  // routes 数组用于指定 hash 地址与组件的对应关系
  routes: [
    // 重定向规则
    { path: '/', redirect: '/home' },
    { path: '/home', component: Home },
    { path: '/movie', component: Movie },
    {
      path: '/about',
      component: About,
      // 这里为子路由设置重定向 将子路由根页面指向 tab1
      redirect: '/about/tab1',
      // 子路由规则
      children: [
        // 注意这里与普通路由路径不同 不需要写 /
        { path: 'tab1', component: Tab1 },
        { path: 'tab2', component: Tab2 }
      ]
    }
  ]
})
```

设置子路由跟页面也可以使用默认子路由代替路由重定向，使用方法为在 children 中定义一个 path 为空的项：

```js
children: [
    { path: '', component: Tab1 },
    { path: 'tab1', component: Tab1 },
    { path: 'tab2', component: Tab2 }
]
```

注意此时也要修改 vue 文件中的路由链接，因为制定了默认路由，tab1 的链接地址可以省略

```vue
<router-link to="/about">tab1</router-link>
```



## 动态路由

### 动态路由 id 传参

目标：解决路由规则复用的问题

应用场景：大量结构重复的页面，只是填充内容不同，例如豆瓣图书页面有很多书目，点击某本图书后根据书显示不同内容

动态路由将 Hash 地址中的可变部分定义为参数项，从而提高路由规则的复用性，在 vue-router 中使用 `:`定义路由的参数项，例如：

定义路由链接：

```vue
<template>
  <div class="movie-container">
    <h3>Movie 组件</h3>
    <router-link to="/movie/movieItem/1">Moive 1</router-link>
    <router-link to="/movie/movieItem/2">Moive 2</router-link>
    <router-link to="/movie/movieItem/3">Moive 3</router-link>
    
    <router-view></router-view>
  </div>
</template>
```

在`index.js`中声明路由规则

```
{ path: '/movie/movieItem/:id', component: MovieItem },
```

在路由规则中的`:id`即表示路由参数，注意这里所有的带有 id 的路由链接都会被指向 MovieItem 这一组件，只是传递进去的参数不同，该参数可以通过`this.$route.params.xxx`查看，其中`this.$route`是路由的参数对象

### 动态路由 props 传参

为路由规则开启 props 传参之后，可以通过 props 属性传递数据（该方法与使用 id 进行参数传递的方式功能一样）：

为组件添加 props 属性：

```
props: ['mid'],
```

为路由规则打开 props 属性：

```
{ path: '/movie/movieItem/:mid', component: MovieItem, props: true },
```

此时`:mid`表示为 props 属性传值，这样，显示 props 属性的时候，直接使用插值表达式等方式即可

### 路径参数和查询参数

hash 地址中 `/`后面的参数项叫做路径参数，路径参数通过 `this.$route.params`来访问路径参数

hash 地址中`?`后面的参数项叫做查询参数，使用`this.$route.query`来使用，例如：

```
<router-link to="/movie/movieItem/1?name=zs age=20">Moive 1</router-link>
```

路由对象中另外两个相似的属性为 fullPath 和 path，其中 fullPath 中保存的是完整地址，例如：

```
fullPath: "/movie/movieItem/1?name=zs%20age%3D20"
```

path 保存的是路径部分：

```
path: "/movie/movieItem/1"
```

## 导航守卫

### vue-router 的编程式导航 API

导航：点击链接实现跳转

两种导航方式：

- **声明式导航：**浏览器中通过点击链接实现导航的方式叫做声明式导航，例如网页中点击 a 标签，在 vue 项目中点击 router-link
- **编程式导航：**浏览器中调用 api 实现的导航，例如网页中通过 location.href 跳转页面的方式

在 vue-router 中，提供了一些编程式导航的 API：

1. `this.$router.push('hash 地址')`：打开新地址，同时增加一条历史记录
2. `this.$router.replace('hash 地址')`：打开新地址，同时用新地址替换之前页面历史
3. `this.$router.go(n)`：向前/向后跳转几个页面，例如 -2 表示后退2个页面，2表示前进两个页面记录

由于实际开发中一般只会用到前进或后退一个页面，vue-router 提供了与 go 方法类似的两个方法：

1. `this.$router.back()`：后退一个页面
2. `this.$router.forward()`：前进一个页面

### vue-router 的导航守卫

导航守卫可以控制路由的访问权限，例如，某些网站在未登录的时候应该无法跳转到主页等内容，此时设置的导航守卫在页面跳转时，如果检测到未登录，会强制跳转到登录页

### 全局前置守卫

每次路由跳转时，都会触发全局前置守卫，全局是指控制所有页面的跳转，前置指在页面跳转之前就会执行相关的处理，使用方法为：

```js
const router = new VueRouter({})
// 定义全局前置守卫 每次页面跳转都会触发 fn 回调函数
router.beforeEach((to, from, next) => {
  // to - 将要访问的路由的信息对象
  // from - 将要离开的路由的信息对象
  // next - 调用 next() 函数表示放行 即允许这次路由跳转
  console.log(to)
  console.log(from)
  next()
})
```

`to` 和 `from`保存了路由的相关信息，可以获取到一些 path 以及 fullPath 等信息，注意如果不调用 `next()`函数会使得页面都无法发生跳转

### next() 函数

对于 next 函数

![image-20221008153659821](vue5.assets/image-20221008153659821.png)

next 函数有三种调用方式：

1. `next()`，直接调用，表示放行，跳转到下一个页面
2. `next('/login')`，带路径参数，表示强制跳转到其他页面
3. `next(false)`：传入 false 参数，表示不允许跳转，保持在当前页面

在使用的时候，要结合跳转的页面以及 token 进行判断，例如：

```js
router.beforeEach((to, from, next) => {
  if (to.path === '/main') {
    const token = localStorage.getItem('token')
    // 判断是否有 token
    if (token) {
      // 有 token 可以跳转到 main 页面
      next()
    } else {
      // 强制跳转到 login
      next('/login')
    }
  } else {
    next()
  }
})
```

