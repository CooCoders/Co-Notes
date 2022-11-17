## 过滤器

过滤器在 Vue3 中已经被移除，其作用在于对文本进行格式化，一般用于两个地方：插值表达式和 v-bind 属性绑定，过滤器添加在 js 表达式的尾部，由管道符进行调用：

```
{{message | capitalize}}
```



## 侦听器

侦听器（watch）监视数据变化，定义在 watch 节点上，因此，**如果需要实时监视某个数据的变化，并且根据数据的变化执行某些操作，需要使用侦听器**

侦听器有两种形式：

- 方法形式
  - 优点：写法简单
  - 缺点：无法监听对象中某个属性的变化（对象的属性变化不会触发）[可以使用 对象.属性 的方式]
  - 缺点：无法实现刷新页面时立即触发侦听器
- 对象形式
  - 优点：immediate 属性可以实现刷新页面的时候立即触发侦听器
  - 优点：deep 属性可以实现对对象中某个属性的侦听

方法方式调用格式为：

```js
<input type="text" v-model="texts">

    watch:{
        // 待监视的数据名与侦听器函数的名
        // newVal 表示新值 oldVal 表示旧值
        // 注意第一个位置的参数才是 newVal，第二个位置的参数表示 oldVal
        // 以位置区分而非参数名区分
        texts(newVal, oldVal){
            console.log(oldVal + '-->' + newVal)
        }
    }
```

可以看出，侦听器本质上是一个与变量名同名的函数

使用方法方式对对象的某个属性侦听：

```js
watch:{
    // 如果要监听对象的子属性值的变化 必须要使用表达式的形式（包裹一层单引号）
    'info.username'(newVal){
        console.log(newVal)
    }
}
```

对象方式侦听对象中某个属性：

```js
watch:{
    info: {
        handler(newVal){
            console.log(newVal)
        },
            // 深度监听 此时监听对象的任意一个属性变化都会触发该侦听器
            deep: true,
           // 立即触发
            immediate: true,
    }
    }
```



## 计算属性

计算属性指的是通过一系列运算之后，最终得到的一个属性值，这个动态计算出来的属性值可以被模板结构或者 methods 方法使用。

需求：根据 data 中的 r，g，b变量改变某个 div 的颜色，此时可以通过模板字符串实现

```html
<!-- :style 绑定一个样式对象 它的值是一个{}样式对象 -->
<div id="show" :style="{backgroundColor: `rgb(${r}, ${g}, ${b})`}">
    <!-- rgb({{r}}, {{g}}, {{b}}) -->
    {{`rgb(${r}, ${g}, ${b})`}}
</div>
```

```js
data: {
    r: 0,
    g: 0,
    b: 0,
},
```

 使用计算属性进行改进：（注意计算属性的定义方式为函数，但是在调试工具中可以看到其返回的结果实际上是以属性方方式存在的）

```html
<div id="show" :style="{backgroundColor: rgb}">
    <!-- rgb({{r}}, {{g}}, {{b}}) -->
    {{rgb}}
</div>
```

```js
// 计算属性挂在 computed 节点下
// 虽然声明为方法 但使用时按照属性格式来使用
computed:{
    rgb(){
        return `rgb(${this.r}, ${this.g}, ${this.b})`
    }
}
```

此时相当于在 data 下面增加一个 rgb 属性，并且会根据数据的变化自动计算新值：

```js
data: {
    r: 0,
    g: 0,
    b: 0,
    // 下面声明的rgb计算属性相当于在 data 节点下挂载一个 rgb
    // rgb: xxx
},
```

**优点：**

- 自动计算
- 方便复用



使用反单引号表示模板字符串，可以使用`$`符号插入变量值：

```
`variable: $(val)`
```

上述代码中的 val 为一个变量，通过`$(val)`可以引用它

## axios

### 基本使用

axios 是一个专注于网络请求的库，例如：

```js
const result = axios({
    methods: 'GET',
    url: 'http://www.liulongbin.top:3006/api/getbooks'
})

// console.log(result)   -- promise 对象
result.then((val)=>{
    console.log(val)
})
```

注意 axios 直接返回的是一个 Promise 对象，并且使用 then 方法打印出的值并非是接口返回的真实数据，axios 会基于接口返回的真实数据再次进行包装，调用`.data`才是获取真实数据

```js
axios({
    methods: 'GET',
    url: 'http://www.liulongbin.top:3006/api/getBooks'
}).then((val)=>{
    console.log(val.data)
})
```

### 传参

发起 get 请求使用 params:

```js
axios({
    methods: 'GET',
    url: 'http://www.liulongbin.top:3006/api/getBooks',
    params: {
        id: 1
    },
}).then((val)=>{
    console.log(val.data)
})
```

发起  post 请求使用 data：

```js
axios({
    methods: 'GET',
    url: 'http://www.liulongbin.top:3006/api/post',
    data: {
        name: 'zs',
        age: 12,
    },
}).then((val)=>{
    console.log(val.data)
})
```

### 使用 async 和 await 简化写法

如果某个方法的返回结果是 promise 实例，那么前面就可以添加 await，注意 await 只能用于 被 async 修饰的方法中：

```js
async function print() {
    const res = await axios({
        methods: 'GET',
        url: 'http://www.liulongbin.top:3006/api/getBooks',
        params: {
            id: 1
        },
    })
    console.log(res)
}
```

如果想要得到接口返回的真实数据 res.data，可以使用对象的解构赋值快速获取：

```js
// 右侧 axios 返回的对象中包含 data 属性，直接使用对象解构赋值方法获取
// data:res 对返回的 data 进行重命名
const {data：res} = await axios({
    methods: 'GET',
    url: 'http://www.liulongbin.top:3006/api/getBooks',
    params: {
        id: 1
    },
})
```

### axios.get() 和 axios.post()

axios 提供了直接发起 get 或 post 请求的方法：

```js
$('#get').on('click', async () => {
    const { data: res } = await axios.get('http://www.liulongbin.top:3006/api/getBooks', {
        params:{
            id: 1,
        },
    })
    console.log(res.data)
})

$('#post').on('click', async ()=>{
    const {data: res} = await axios.post('http://www.liulongbin.top:3006/api/post',{
        username: 'zs',
    })
    console.log(res.data)
})
```

注意两种方式的参数传递不同，get 类型需要指定 params 对象，post 方式忽略

## vue-cli

### 基本概念

**单页面应用程序（SPA）：**Web 网站中只有唯一的一个 html 页面，所有功能的交互都在这唯一的一个页面中完成（开发难度高）

**vue-cli：**vue-cli 是 vue.js 开发的标准工具，自动完成基于 webpack 的 vue 项目搭建

### 安装

vue-cli 是 npm 上的全局包，安装命令为：

```
npm install -g @vue/cli
```

测试使用`vue-V`命令

### 使用

1. 基于 vue-cli 快速生成工程化的 vue 项目（可以进入要创建项目的文件夹后，在地址栏中输入 cmd 打开终端）

   ```
   vue create PROJ_NAME
   ```

2. Pick a preset：

   1. Default Vue2
   2. Default Vue3
   3. Manually select features： 建议选择，可以根据需要定制安装功能

3. Check the features：

   1. 可以根据自己需要选择安装功能

4. 根据之前选择安装的 feature 选择进行更具体定制，例如：

   1. 选择 vue 版本
   2. 选择 css 预处理器等等

5. Where do you prefer placing config for Babel, ESlint .... 如何创建 babel，eslint 等的配置文件：

   1. in dedicated config files：创建独立配置文件（推荐）
   2. in package.json：放在 package.json 文件中
   
6. 是否将上述选择保存到一个 preset 中
   
   1. 这里可以将所有选择进行保存，如果选择保存，需要键入一个 preset 名称
7. 创建项目
   
   1. 注意在安装过程中不要碰到终端窗口，否则容易出现“冻结”现象，此时选中终端，使用`Ctrl + C`恢复安装


vue-cli 的版本不同，中间过程会稍有不同，例如 v5.0.8 会选择使用 yarn 或者 npm，随后根据选择结果提供启动项目的指令：`npm run serve`

启动项目：`npm run serve`

### 目录构成

注意如果打开项目发现所有 vue 文件没有代码高亮，可以搜索安装 Vue language features 插件

- node_modules
- public
  - 图表
  - index.html
- src
  - assets：静态资源
  - components：封装的一些可复用的组件
  - main.js：项目的入口文件，项目会先执行此文件，也是最先开始打包的文件
  - app.vue：项目的根组件
- package.js ...

### vue 项目运行流程

核心工作：通过 main.js 将 App.vue 中的内容渲染到 index.html 的指定区域中

其中： 

- App.vue 用来编写带渲染的模板结构
- index.html 中需要预留一个 el 区域
- main.js 把 App.vue 渲染到 index.html 所预留的区域中


## Vue-组件

### 组件化开发

根据封装的思想，将页面上可以重用的 UI 结构封装为组件，从而方便项目的开发和维护

vue 是一个支持组件化开发的前端框架，**vue 中规定，组件的后缀是 `.vue`**，例如 `App.vue` 实质上就是一个 vue 的组件

### Vue 组件组成部分

每个组件都由三部分组成：

- template ：组件的模板结构  --- dom （**注意template中只能有一个根元素**）
- script：组件的 js 行为
- style：组件的样式  --- style

页面一般为 template  -- script  -- style 顺序排列

注意在绑定数据的时候，在组件中不能像之前那样指向一个对象，必须要用函数的方式：

```js
<script>
// 默认导出 固定写法
export default {
    /* 组件中的data不能像之前一样指向一个对象，要指向一个方法 */
    /* data: {
        name: 'div1'
    } */
    data(){
        // return 出的对象可以定义数据
        return {
            name: 'div1'
        }
    }
}
</script>
```

### 组件中定义 methods 方法

使用 methods 方法与之前方法一致：

```js
methods:{
    changeName(){
        // 指向 VueComponent 实例对象
        console.log(this)
        this.name = 'DIV'
    }
}
```

其他的方法，例如 侦听器，过滤器以及计算属性等，定义都与上述一致

### 启用 less 语法

```html
<style lang="less">
    .div1 {
        margin: 10px;
        background-color: azure;
        border: 1px solid black;
        p{
            color: red;
        }
    }
</style>
```



### 使用组件的三个步骤

1. 使用 import 语法导入需要的组件
2. 在 components 节点注册组件
3. 以标签形式使用组件

例如：（注意这里的`@`符号已经默认配置为 src 文件夹，可以直接使用）

```vue
<template>
  <div class="app-container">
    <h1>App 根组件</h1>
    <hr />

    <div class="box">
      <!-- 渲染 Left 组件和 Right 组件 -->
      <!-- 使用标签形式使用组件 -->
      <Left></Left>
      <Right></Right>
    </div>
  </div>
</template>

<script>
// 1.import 导入所需使用的组件
import Left from '@/components/Left.vue'
import Right from '@/components/Right.vue'

export default {
// 2.在 components 节点注册组件
  components:{
    Left,
    Right,
  }
}
</script>
```

Tip：安装自动提示路径的 vscode 插件 

1. 查找安装 Path Autocomplete 插件

2. 在 Setting （左下角齿轮）中打开 setting.json 文件（右上角打开设置json），并添加：

   ```json
   "path-autocomplete.extensionOnImport": true,
   "path-autocomplete.pathMappings": {
   "@": "${folder}/src"
   },
   ```



### 注册全局组件

通过 component 注册的是私有子组件，如果在 A 组件中注册组件 C， 则组件C只能用于组件A，如果某个组件频繁被使用，可以使用全局注册的方式，注册方式为：

在 main.js 中使用 Vue.component() 注册，例如：

```js
import Vue from 'vue'
import App from './App.vue'
// 导入需要全局注册的组件
import Test from '@/components/test.vue'

Vue.config.productionTip = false
// 参数一为 字符串格式的注册组件名称（相当于起别名）
// 参数二为 全局注册组件名称 即上面导入的名称
Vue.component('Test', Test)

new Vue({
  render: h => h(App),
}).$mount('#app')
```

### 组件的 props

props 是组件的自定义属性，在封装组件的时候，合理地使用 props 可以提高组件地复用性

使用 props：

```js
export default {
    // 定义位置
    props: ['initNum'],
    data() {
        return {
            count: 0
        }
    },
    methods: {
        add() {
            this.initNum += 1
        }
    }
}
```

注册完组件后，可以在使用组件的地方通过属性传递此自定义属性：

```vue
<Count initNum="3"></Count>
```

注意通过这种方式传入的值是字符串形式，如果需要传入数值，需要通过 v-bind 的方式（动态属性绑定双引号中是 js 表达式，所以此时传入的内容是数字形式）：

```vue
<Count :initNum="3"></Count>
```

注意点：

- props 中的数据可以像 data 中定义的数据一样用于插值表达式等地方

- **只读：**虽然可以直接对 props 中的数据进行修改，但是 vue 并不推荐这种方式，因此可以认为 **props 是只读的**，即只能渲染到页面上，所以如果需要修改 props 中的值，需要借助 data：

  ```js
  props: ['initNum'],
      data() {
      return {
          count: this.initNum
      }
  },
  ```

- **定义默认值：**为 props 定义初始值，如果需要使用 props 中的数据初始化 data 中的某个数据，那么需要使用组件的时候必须传入初始值，为了防止未指定初始值带来数据 undefined 的问题，需要为 props 属性指定 default 值：

  ```js
  // 为 props 属性定义初始默认值，需要改写为对象形式
  props:{
      initNum:{
          default: 0
      },
      /* B:{
          default: 0
      } */
  },
  data() {
      return {
          count: this.initNum
      }
  },
  ```

- **规定类型：**，规定传入的 props 属性必须为某个属性，例如：

  ```js
  props:{
      initNum:{
          default: 0,
          type: Number/Boolean/String/Array/Object
      },
      /* B:{
          default: 0
      } */
  },
  ```

- **规定必须传值：**使用 required 属性可以规定使用组件时候是否需要对指定的 props 属性传值：

  ```js
  props:{
      initNum:{
          default: 0,
          type: Number/Boolean/String/Array/Object,
          required: true
      },
  },
  ```

### 组件冲突

默认情况下， vue 文件中的样式会全局生效，因此容易造成多个组件之间的样式冲突问题，其原因在于：

- 单页面应用程序中，所有组件的 DOM 结构，都是基于唯一的 index.html 页面进行呈现的
- 每个组件中的样式，都会影响整个 index.html 页面中的 DOM 元素

解决方法，为一个组件中的所有的元素添加唯一属性，例如 `data-v-1`,`data-v-2`，然后在定义属性的时候，使用带属性的选择器定义样式：

```
p[data-v-1]{ ... }
```

Vue 提供了这一功能，需要在 vue 文件的 style 中指定 `scoped`即可，例如：

```vue
// 指定 scoped 后 可以看到当前页面中定义的 DOM 元素都有唯一的一个属性值
<style lang="less" scoped>
.left-container {
  padding: 0 20px 20px;
  background-color: orange;
  min-height: 250px;
  flex: 1;
}

h3 {
  color: red;
}
</style>
```

### 样式穿透

#### 需求场景

组件A和组件B为兄弟组件，并且组件A和B都导入了组件C，现在需要在组件A中修改组件C的某个 DOM 元素样式（组件B中的样式不变）（例如组件C中定义了 h4，需要修改组件A中的 h4 颜色，而保持组件B中的 h4 样式不变），由于是修改某个特定组件的样式，因此会在组件A的 style 中使用 scoped，但是此时发现并未有效。

#### 原因

scoped 会在当前组件的所有元素后添加唯一属性，因此在组件A的样式中定义的属性实际上是 `h4[xxxxx]{}`的形式，而实际需要改变的组件C中的样式为 `h4{xxxx}`，因此并未匹配

#### 解决方法

使用样式穿透，在组件A中的样式中使用:

```
/deep/ h4{xxxxx}
```

此时在页面中审查 DOM 元素，发现组件A中的 h4 元素样式名变为 `[xxxxx] h4{}`，相当于在组件A的范围内向下查找 h4 元素，因而有效

#### 应用

如果使用第三方vue组件库，并且需要对某个组件的样式进行修改，需要进行样式穿透才能修改

### 组件的实例对象

- 由于浏览器无法直接识别 vue 文件，所以实际运行的时候需要 complier 进行解析转变为 js 然后加载到页面中
- vue文件相当于一个模板，只有在以标签形式真正使用的时候，才会实例化

## 生命周期

生命周期（Life Circle）：是指一个组件从创建 -- 运行 -- 销毁的整个阶段，其强调的是一个时间段

生命周期函数：vue 框架提供的内置函数，会伴随着组件的生命周期，自动按次序执行，例如：

- 组件创建阶段
  - beforeCreate：创建之前
  - created：创建时（此时只存在于内存）
  - beforeMount：渲染之前
  - mounted：渲染时
- 组件运行阶段
  - beforeUpdated：更新之前
  - updated：更新时
- 组件销毁阶段
  - beforeDestroy：销毁之前
  - destroyed：销毁时

整个过程：

<img src="./vue3.assets/image-20220930151822272.png" alt="image-20220930151822272" style="zoom: 45%;" />

也可以参考 vue 文档的流程图：

> https://vuejs.bootcss.com/guide/instance.html

代码示例：

```js
export default{
    props:{
        init:{
            default: 0,
        }
    },
    data(){
        return 
    },
    methods: {
        show(){
            console.log('function show.')
        }
    },
    // 使用较少
    beforeCreate() {
        // 此时组件的 props data methods 都尚未创建，处于不可用状态
    },
    // 使用较多
    created() {
        // 组件的 props 等都创建完毕，处于可用状态，但是组件的模板结构尚未生成
        // 可以通过 ajax 请求数据
        // 由于尚未渲染页面 无法使用 DOM 结构
    },
    // 使用较少
    beforeMount() {
        // 页面上没有 DOM 结构 无法操作 DOM
    },
    // 使用较多
    mounted() {
        // 将内存中的 html 结构，渲染到浏览器中，此时浏览器包含当前页面的 DOM 结构
    },
    beforeUpdate() {
        // 将要根据变化后的数据重新渲染组件的页面结构
        // 此时取得数据是最新的 但是直接从页面结构中拿到的数据是旧的
    },
    updated() {
        // 完成页面结构的渲染
    },
    beforeDestroy() {
        // 将要销毁还尚未销毁 此时组件的结构依然可以正常使用
    },
    destroyed() {
        // 完成销毁 DOM 结构被移除
    },
}
```

## 传值

组件之间的关系：

- 父子关系
- 兄弟关系

### 父子组件数据共享

#### 父 ->子共享

父组件向子组件共享数据需要使用自定义属性 props，例如：

子组件中定义 props：

```vue
<template>
    <div>
        <div class="con">
            <p>son test sentence {{msg}} -- {{info}}</p>
        </div>
    </div>
</template>

<script>
export default{
    props: ['msg', 'info'],
}
```

父组件使用子组件的时候传入 props 值：

```vue
<template>
    <div>
        <div class="container">
            <p>father test sentences</p>
            <son :msg="passVal" :info="user"></son>
        </div>
    </div>
</template>
```

注意：

- 注意传值的时候如果使用 v-bind 方式，传递的是对应的属性的值，如果不使用 v-bind 的方式，传递的是字符串
- 在传递对象的时候，实际上传递到是对象的引用
- 如果需要修改传递的数据，不要直接修改 props 中的数据（保持只读），应修改 data 中的属性，间接修改 props 的数据

#### 子 -> 父共享

子组件向父组件传递要使用自定义事件，语法格式为：

子组件部分：

```js
methods: {
    add(){
        this.count += 1
        // 第一个参数为绑定的自定义函数名
        // 第二个参数为要传递的值
        this.$emit('countChange', this.count)
    }
}
```

父组件部分：

```js
// 使用 countFromSon 接收来自子组件的值
<p>Father Component: {{ countFromSon }}</p>
// 格式为 @自定义函数=处理函数名 
<Test @countChange="getNewCount"></Test>

// 定义处理函数逻辑
methods:{
    getNewCount(val){
        this.countFromSon = val
    }
}
```

父组件的处理过程与定义 click 等方式一样，整个过程相当于将 js 内置的 click 等函数改为自定义的函数

### 兄弟组件数据共享

Vue2 中兄弟组件之间共享数据使用 eventBus

EventBus 的使用流程：

- 创建 eventBus.js 模块，并向外共享一个 Vue 的实例对象

  ```js
  import Vue from 'vue'
  
  // 向外暴露 Vue 的实例对象
  export default new Vue()
  ```

- 在数据发送方，调用 bus.$emit(事件名称， 发送数据)方法触发自定义事件

  ```js
  // click 触发 发送事件
  <button @click="sendMsg">Send</button>
  
  // 导入 bus
  import bus from '@/components/eventBus.js'
  
  // 使用 $emit 发送
  export default {
    methods: {
      sendMsg(){
        bus.$emit('share', this.msg)
      }
    }
  }
  ```

- 在数据接收方，调用 bus.$on(事件名称， 事件处理函数)方法注册自定义事件

  ```js
  import bus from '@/components/eventBus.js'
  
  export default {
    // 在 created 阶段定义
    // 使用 $on 方法定义触发时的逻辑
    created() {
      bus.$on('share', val => {
        this.reciveMsg = val
      })
    }
  }
  ```

即整个过程分为三部分：send -> bus -> receive，整个数据发送以及接收的方法作为一种现成的解决方案，直接调用使用即可

