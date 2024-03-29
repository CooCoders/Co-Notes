Vue3 + Electron



App 开发：

- 原生 App 开发技术：性能好，可以直接读取本地设备，如果针对不同平台，需要针对性开发（Android、IOS）
- Web App（H5）：跨平台好，性能较差，不能直接调用本地设备资源（例如摄像头等）
- Hybrid App：结合上述两部分优势，例如微信小程序等

Electron：将 H5 页面嵌入到一个桌面端的壳里面，从而打包成一个 .exe 或者 .pkg 包，像软件安装一样使用 Electron

## Vite

原生 JS 对模块化开发的方式支持并不友好，当项目的模块数增多以后，存在两个问题：

- 服务器启动缓慢
  - 打包器（构建整个应用、源码处理）- 冷启动
- 项目更新缓慢
  - HMR，只更改修改模块

Vite 对上述两个问题进行了改进

## 基础设置

### VSCode 设置

常用插件：

- Chinese - 语言插件
- Live Server - 在本地开启 server
- Monokai Pro - 编辑器皮肤
- Vetur - Vue 构建辅助工具（注意如使用 vue3， 需卸载此插件并安装 Volar）

编程字体： Fira Code

安装字体后，在设置中 Font family 项前添加 `Fira Code Light `,`fira code`

下载字体可用：

> https://www.fonts.net.cn/font-32128192175.html

### 常用的包管理工具

- npm
- yarn
- pnpm（vite 推荐安装工具）
  - pnpm 由 npm/yarn 衍生而来，解决了 npm 和 yarn 的内部潜在 bug，并且极大地优化了性能，拓展了使用场景

## 搭建 Vite 项目

初始化项目，首先使用命令：`pnpm create vite`，之后的几步是：

- Project Name：输入项目名称（直接输入即可）
- Select a framework：选择项目框架（可选择有 vanilla、vue、react等）
- Select a variant：选择框架变体
- 此时提示： `Done. Now run`，需要移动到项目下 `cd PROJECT_NAME`
- 安装所需依赖：`pnpm install`
- 启动项目：`pnpm run dev`
- [ 注意上述命令中 `npm/yarn/pnpm`可互相替代 ]

### 项目结构

几个重要文件结构：

- public目录：目录下的文件是可以直接访问的（在根路径后直接跟文件名）
- src目录：存放所有的源代码文件
  - assets目录：存放资源
  - components目录：组件
  - App.vue、main.js
- index.html：程序入口文件
- vite.config.js：vite 的配置文件
- pnpm-lock.yaml：pnpm 的依赖，不要修改

### 引入

如果出现 import 导入语法错误，检查引入 `main.js`：

```
// cannot use import statement outside a module
<script type="module" src="/src/main.js"></script>
```

引入插件：

```vue
<script setup>
	import Test from './components/views/Test.vue'
</script>
```



### NPM 依赖解析和预构建

预构建过程的两个目的：

- CommonJS 和 UMD 兼容性：将 CommonJS 等导入模块的语法自动转化为 esm 语法，例如对于 vue 的引入：

  ```
  import { createApp} from 'vue'
  ```

- 提高性能：如果要导入的一个模块内文件较多，会发出较多的 http 请求，造成浏览器阻塞，预构建过程中会将这些文件打包成一个模块进行请求

### esbuild 打包器

esbuild 是一个 JS 打包工具，vite 也使用了该工具，esbuild 的特点有：

- 极快的速度而不需要缓存
- ES6 和 CommonJS 模块支持
- ES6 模块的 tree shaking
- TS 和 JSX 语法支持
- Source maps 支持
- 简化打包
- 插件支持

### vue3 第一个例子

vue3 语法上对 vue2 组件进行了简化，例如：

```vue
<!-- test vue component -->
<template>
  <div>
    <h3>this is a test setence</h3>
    <p>{{ count }}</p>
    <button @click="add">add</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
// 将 count 实现为 RefImp
const count = ref(0)
console.log(count)
const add = () => {
  count.value += 1
}
</script>
<style lang="stylus" scoped></style>
```

### 关于快速生成 vue3 文件模板

可在 设置 - 配置用户代码片段 中设置自定义的模板，输入自定义的命令名称（如果没有会自动生成 json 文件），并在该 json 文件中写入：

```json
注意第一个花括号在模板中已经自动生成
{
  "Print to console":{
    "prefix": "vue3",

    "body": [

      "<template>",

      "  <div>",

      "",

      "  </div>",

      "</template>",

      "",

      "<script setup lang='ts'>",

      "import{ref,reactive} from 'vue'",

      "",

      "</script>",

      "<style scoped>",

      "",

      "</style>",

    ],

    "description": "Log output to console"

  }

}
```



## Vue3 

### 基础概念

#### Vue3 的核心功能

- 声明式渲染：可以声明式描述输出的 HTML 和 JS 之间的关系
- 响应性：自动追踪 JS 状态并在发生变化的时候响应式更新 DOM

#### 单文件组件

`.vue`文件即单文件组件，单文件组件会将一个组件的逻辑、模板和样式都封装在一个文件中

####  mount 方法 - 挂载

应用实例必须在调用 mount 挂载之后才会被渲染出来，该方法接收一个容器参数，该参数可以是一个 DOM 元素或者一个 CSS 选择器字符串，例如

```
<div id="app"></div>

app.mount('#app')
```



#### 其他概念

- 语法糖：Syntactic Suger，就是在不改变程序运行结果的前提下，对代码的写法进行改变，提高代码的可读性，类如汉语的成语
- vue3 setup script：用于简化写法的 vue3 语法糖，添加 setup 的 script 标签会自动将所有的顶级变量和函数暴露给 template 使用，无需手动引入组件以及 export default
- 钩子函数

### 模板语法

在底层机制中，Vue 会将模板语法编译成为高度优化的 JS 代码，并结合响应式系统，当应用状态改变的时候，推断出最少需要重新渲染的组件数量，减少 DOM 操作







### ref 和 reactive

数据响应式：响应是指，修改数据之后，视图随之更新（vue 通过为每个传入的数据添加 getter 和 setter 方法，以此追踪每个数据）

res 和 reactive 都用于定义响应式数据，其中 reactive 更推荐定义复杂数据类型，ref 则更推荐定义简单数据类型（ref 是对 reactive 的二次封装，因此在使用 ref 定义引用数据类型的时候，内部会再调用 reactive 实现响应式，使用 ref 定义的数据在访问的时候要使用`.value`获取）

对于 `.value`：

对于 ref 定义的简单数据，在操作数据的时候需要使用`.value`，在读取数据中，模板会默认调用 value 属性，因此无需添加

对于 reactive 定义的复杂数据类型，操作与使用均无需使用`.value`

### 内容渲染指令

#### 插值表达式

插值表达式，这部分与 vue2 的语法相同，例如：

```vue
<p>this is a test count {{ count }}</p> 

<!-- 此时显示的结果为 1 -->
<p>Number: {{ count + 1 }}</p>

const count = ref(0)
```

插值表达式中也可以进行简单的逻辑运算，例如：

```vue
<p>{{ count == 0 ? 'Yes' : 'No' }}</p>
```

`v-html`可以渲染 html 内容，例如：

```vue
<div v-html="hContent"></div>

const hContent = ref('<h3>Test h3 title</h3>')
```

此时 div 的内容以 h3 元素呈现

### 属性绑定指令

通过属性绑定指令`v-bind`为标签元素属性动态赋值，例如：

```vue
<p v-bind:title="textTips">some texts</p>
<input type="text" v-bind:placeholder="notionText" />

const textTips = ref('this is a p label')
const notionText = ref('some texts')
```

可以使用`v-bind`为标签同时绑定多个属性，例如：

```vue
<div v-bind="propObj">div</div>

const propObj = ref({
  id: 1,
  name: 'test',
})
```

此时在浏览器中可以看到 div 元素增加了 id 和 name 属性

v-if 指令：通过 v-if 指令可以动态显示/隐藏元素，例如：

```vue
<button @click="chgShow">show</button>
<div class="test" v-if="isShow"></div>

const chgShow = () => {
  isShow.value = !isShow.value
}
```

此时点击按钮改变 isShow 的值， div 也会随之改变状态

### 事件绑定指令

通过 `v-on`指令为元素绑定事件（也可以简写为`@`），例如：

```vue
<button v-on:click="add">add</button>
<p>Number: {{ count }}</p>

const add = () => {
  count.value += 1
}
```

例子：监听输入框内容并进行输出

```vue
<input type="text" @:keyup="handleKeyUp" />
<p>input content:{{ inputContent }}</p>

// 使用事件对象e获取触发对象
const handleKeyUp = (e) => {
  inputContent.value = e.target.value
}
```

`v-on`指令的完整格式为：

```
v-on:submit.prevent="onSubmit"
v-on:参数.修饰符=事件触发函数
```

`v-on:input`用于文本框输入

vue3 也支持为一个事件绑定多个处理函数，例如：

```vue
<button @click="print1(), print2()">cli</button>

const print1 = () => {
  console.log('11111')
}

const print2 = () => {
  console.log('22222')
}
```



### 修饰符

例如处理冒泡事件：

```vue
<div class="bubble-test" @click="alertDiv">
    <button @click="alertBtn">test</button>
</div>
```

此时由于冒泡机制，点击按钮时候会先后触发按钮和 div 的点击事件，如需要阻止冒泡，可以添加 stop 修饰符：

```vue
<button @click.stop="alertBtn">test</button>
```

其他常见的事件修饰符还有 prevent（阻止默认事件，例如表单提交等）以及 once（设置绑定的事件只执行一次），常用的案件修饰符有 enter 和 esc ，表示监听到按下 enter 和 esc 键的时候触发，例如：

```vue
<input type="text" @keyup.esc="clear" />

const clear = (e) => {
  e.target.value = ''
}
```

修饰符与 vue2 相同，	具体解释见 vue2 的笔记



### 响应式

通过 ref 和 reactive 都可以实现响应式数据

通过 ref 方式：

```vue
<button @click="add2">{{ count }}</button>

const count = ref(0)
const add2 = () => {
  count.value += 2
}
```

通过 reactive 方式：

```vue
<button @click="add">{{ state.count }}</button>

// reactive 需要定义为对象形式
const state = reactive({ count: 5 })
const add = () => {
  state.count++
}
```

### 双向数据绑定指令

使用`v-bind:value`只能由数据源向页面渲染，在页面修改的数据无法影响数据源，此时可以使用`v-model`指令，该指令可以用于 input、textare 以及 select 元素，例如：

```vue
<input type="text" v-model.number="val1" />

const val1 = ref(150)
```

此时 val1 与 input 输入框中的数据同步，一方修改，另一方也会修改，`.number`表示将输入的数据自动转化为数字类型，类似的修饰符还有`lazy`，表示全部输入完后再修改数据，`.trim`自动去除前后空白字符

`v-model`指令常用于文本输入框（text、textarea）以及单选按钮和下拉列表中，例如：

```vue
<!-- 所有radio同时绑定到一个变量 picked 上 此时实现的是单选 -->
<div>
    {{ picked }}
    <input type="radio" value="One" v-model="picked" /> One
    <input type="radio" value="Two" v-model="picked" /> Two
    <input type="radio" value="Three" v-model="picked" /> Three
</div>

const picked = ref('One')
```



```vue
<div>
    {{ selected }}
    <input type="checkbox" value="one" v-model="selected" /> one
    <input type="checkbox" value="two" v-model="selected" /> two
    <input type="checkbox" value="three" v-model="selected" /> three
</div>

// 多选形式
const selected = ref([])
```

下拉列表：

```vue
{{ selectedVal }}
<select v-model="selectedVal">
    <!-- value 也可以不写 此时选择的值就是文本 -->
    <option value="0">beijign</option>
    <option>nanjing</option>
    <option>dongjing</option>
</select>

const selectedVal = ref()
```



### 计算属性

计算属性用于通过一系列计算，得到的一个属性值，例如组件中有一个 count，现在需要对 count 做一些计算得到 newCount，此时可以通过计算属性得到，定义格式例如：

```vue
// 需导入 computed
import { ref, reactive, computed } from 'vue'
// 将计算函数作为 computed 的参数
const dcount = computed(() => {
  return count.value * 2
})
```

使用计算属性可以复用计算，虽然使用函数的方式也可以实现计算属性的功能，但是使用函数的方式，会随着页面渲染而重复执行（例如某个数据改变引起页面渲染），重复的无意义的计算会消耗资源，而计算属性方式定义的结果不会重复计算（计算属性会根据数据变化进行缓存，如没有变化，则不会重复计算）

### class 和 style 的动态绑定

元素可以通过`v-bind:class=""`或者`:class=""`实现动态改变样式，例如：

```vue
<!-- class 如果有多个样式设置需写成列表形式
     通过改变 tag 为 true 或者 false 控制 chgColor 样式是否表示-->
<!-- style 需要按照以键值对的形式写成小驼峰形式 -->
<p :style="{ textDecoration: ul }" :class="[chgFont, { chgColor: tag }]">
    
const chgFont = ref('chg-fontsize')
const ul = ref('underline')

.chgColor {
   color: red;
}

.chg-fontsize {
   font-size: 20px;
}
```

使用案例：实现一个列表，点击列表项后当前列表项高亮显示：

```vue
<template>
  <div>
    <ul>
      <li
        v-for="item in usrList"
        :class="{ active: item.id === curInd }"
        @click="chgIndex(item.id)"
        :key="item.id"
      >
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
const usrList = ref([
  { name: 'zs', id: 1 },
  { name: 'ls', id: 2 },
  { name: 'ww', id: 3 },
])
const curInd = ref(1)

const chgIndex = (ind) => {
  curInd.value = ind
}
</script>
<style scoped>
.active {
  background-color: steelblue;
}
</style>
```

（这里有个问题，如果动态决定某个样式类是否展示，那么该样式名就无法使用`-`，例如`chg-color`，此时会报错）

###  条件渲染

`v-if`指令以及配套的`v-else-if`，`v-else`可以实现按一定条件渲染页面，例如，按照不同分数输出对应的字符：

```vue
<p>
    <span v-if="point <= 3">A</span>
    <span v-else-if="point <= 6">B</span>
    <span v-else>C</span>
</p>
```

如果需要多个元素同时由一个变量决定是否显示，可以使用临时标签 `<template>`包裹，例如：

```vue
<template v-if="tag">
	<p>pppp1</p>
	<p>pppp2</p>
</template>
```

### 列表渲染

使用`v-for`进行列表渲染，例如：

```vue
<ul>
    <!-- 注意三个形参的顺序是 value 、key、 index -->
    <li v-for="(v, k, i) in bookList[0]">{{ i }}--{{ k }}--{{ v }}</li>
</ul>

<ul>
    <li v-for="item in bookList">
        {{ item.bookname }}--{{ item.author }}--{{ item.date }}
    </li>
</ul>

const bookList = reactive([
  {
    bookname: 'xiyouji',
    author: 'wu',
    date: '1900',
  },
  {
    bookname: 'shuihuzhuan',
    author: 'shi',
    date: '1900',
  },
])
```

### 生命周期

需求场景：两秒之后输出一个字符

该需求可以通过 setTimeout 函数实现，但实际开发中不推荐使用此方法，因为每次页面渲染都会申请内存，最终可能会导致内存溢出，此外在获取页面数据的时候由于页面渲染的事件不确定，可能会造成一些错误

生命周期函数是 vue 框架内置的一些函数，并伴随着组件的生命周期自动执行，这部分与 vue2 相同，使用方法例如：

```vue
import { onMounted, onBeforeMount } from 'vue'

// 后执行
onMounted(() => {
  console.log('mounted')
})

// 先执行
onBeforeMount(() => {
  console.log('before mounted')
})
```

### 侦听器

侦听器功能与 vue2 相同，在数据发生变化的时候执行，可以使用的钩子有两个`watchEffect`和`watch`：

- watch：之追踪具体的数据源，只有在数据源发生变化的时候才会触发回调（如果某个数据值被操作但未发生值的变化也不会触发），用于更精确地控制回调函数的触发
- watchEffect：自动追踪所有的的数据变化（注意必须要引用一个变化数据才会执行）

watchEffect 例如：

```vue
<p>{{ count }}</p>
<button @click="add">Add</button>

// 第一个参数也可以写成函数形式只要最终返回要监听的数据即可
watch(count, () => {
   console.log('new count', count.value)
})
watch(
  () => {
// return value
    return count.value
  },
  () => {
    console.log('count: ', count.value)
  }
)
```

watch 例如：

```vue
watchEffect(() => {
  // 必须至少引用一个数据变化才会执行回调
  console.log(count.value)
})
```

### 组件注册

组件允许我们将页面 UI 划分为独立可复用的部分，组件在应用程序中经常被组织为层层嵌套的树状结构：

```
Root - Header
     - Main - Content1
            - Content2
     - Aside - Item1
             - Item2
             - Item3
```

全局组件在 `main.js`文件中引入：

```js
import GlobalComp from './components/GlobalComp.vue'

// createApp(App).mount('#app')
const app = createApp(App)
// 注册全局组件
// 第一个参数是组件使用名称
// 第二个参数是组件导入名称
app.component('GC', GlobalComp)
app.mount('#app')
```

经全局注册的组件在项目任何组件内都无需再导入，直接使用即可

局部组件在需要使用的组件内使用`import xxxx from xxx`语法导入

### 组件信息传递

#### 父组件 -> 子组件

首先在父组件中使用子组件的标签中自定义属性：

```vue
// count 保存要传递的数据
<Child1Vue :cou="count"></Child1Vue>

// count 必须在父组件中有定义
const count = "xxxxx"
const count = 12
```

如果只是传递简单的字符串，无需使用 v-bind:

```vue
<Child1Vue text="this is a string"></Child1Vue>
```

随后在子组件中获取、使用该自定义属性值：

```vue
<template>
  <div class="main1">
    <!-- 使用 -->
    <p>Child component 1 --- {{ cou }}</p>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
// 以数组形式接收属性值
defineProps(['cou'])
</script>
```

另一种接收形式：

```vue
<template>
  <div class="main2">
    <p>Child component 2 -- {{ receives.cou }}</p>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
const receives = defineProps(['cou'])
</script>
```



#### 子组件 -> 父组件

子组件向父组件传递数据使用自定义事件，首先在父组件中定义自定义事件：

```vue
<Child1Vue :cou="count" @ch1-event="handleCh1"></Child1Vue>
```

然后在父组件中定义接收函数：

```vue
const handleCh2 = (msg) => {
  // console.log(msg.value)
  ch2val.value = msg.value
}
```

子组件中定义 emit：

```
const emit = defineEmits(['ch1-event'])
 
 // 发送位置
 emit('ch1-event', count)
```

### 事件

简化子组件向父组件传值的过程：使用`v-model`指令

首先父组件中定义：

```
<Child1Vue v-model:sendVal="chVal"></Child1Vue>
```

与使用自定义方法传值一样，v-model指令后面`sendVal`用于子组件中，`chVal`在父组件中接收子组件传来的值（**注意这里的 chVal 必须在父组件中已经定义并使用**，否则无法显示）

随后子组件中定义传值的方法：

```
// 固定格式写法: update:xxxx
const emit = defineEmits(['update:sendVal'])

// 这里传递的为值 而非引用
emit('update:sendVal', val.value)
```

### 透传 fallthrough attribute

在 vue3 中，class 被看作是普通属性，在父组件中，使用子组件时候传入的 class 或者自定义属性都会默认挂在子组件的根 div 上，例如：

```vue
<ChildVue class="a" title="test"></ChildVue>
```

此时子组件中，顶层的 div 自动会带 class 和 tile 属性，如果要关闭这一功能，可以在子组件上设置：

```vue
<script>
export default {
  inheritAttrs: false,
}
</script>
```

使用 useAttrs 钩子可以调用这些属性：

```vue
import { ref, reactive, useAttrs } from 'vue'
const attrs = useAttrs()
console.log(attrs.class)
```

### 插槽 slot

在使用子组件时，像 h5 标签一样传入内容，`<Comp>ABCD</Comp>`，此时在子组件中添加`<slot></slot>`标签，会将内容`ABCD`渲染到子组件上

#### 具名插槽

**用于子组件渲染父组件的内容**

在父组件中，定义：

```vue
<template v-slot:text2>
	<div>text2 from parent</div>
</template>
```

注意这里必须要使用`template`标签包裹，否则报错，`v-slot:text2`制定了 slot 的名称，在子组件中使用：

```vue
<slot name="text2"></slot>
```

即可渲染出对应的父组件的内容，`v-slot`指令也可以简写为`#`，并且在父组件中定义的样式也会被渲染到子组件中

此外，在子组件中，可以向 slot 中添加内容，`<slot>somt text from child</slot>`，如果没有从父组件中获取到数据，就会显示该内容

#### 作用域插槽

**用于父组件渲染子组件的内容**

在子组件中定义：

```vue
<slot :mark="mark1" :mark2="mark2"></slot>
```

在父组件中使用：

```vue
<template v-slot:default="data">
	<p>{{ data.mark }} -	-- {{ data.mark2 }}</p>
</template>
```

也可以使用解构赋值的方式获得从子组件中传入的内容：

```vue
<template v-slot:default="{ mark, mark2 }">
	<!-- mark, mark2 分别对应子组件中定义的 :mark :mark2 -->
	<p>{{ mark }} --- {{ mark2 }}</p>
</template>
```

### 依赖注入 inject

依赖注入是另一种传递数据的方法，可以方便的由父组件向后代组件传值

Provider 组件（父组件）：

```vue
<script>
    import { provide } from 'vue'

    const text = ref('original texts')
    provide('txt', text)
</script>
```

Injector 组件（后代组件）：

```vue
<script>
    import { ref, reactive, inject } from 'vue'

    // 名称要与 provider 组件中提供的名称相同
    const text = inject('txt')
</script>
```

### 组合式函数 composition function

为了方便功能拆分，可以按照不同的操作对象对文件进行拆分，例如，现有功能场景：页面有一个计数器，点击按钮计数器加一，同时，页面的标题也随之变为当前的计数器值

此需求可以在单 vue 文件中实现：

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="add">add</button>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, onUpdated } from 'vue'

// 页面 count 管理
const count = ref(0)
const add = () => {
  count.value++
}

// title 相关功能
onMounted(() => {
  document.title = count.value
})

onUpdated(() => {
  document.title = count.value
})
</script>
<style scoped></style>
```

上述文件中有两个操作对象：count 与 document.title，因此可以将这两部分代码剥离出来，形成两个文件：

`useCount.js`文件，用于处理 count 相关的逻辑：

```js
import ref from 'vue'
const useCount = () => {
  const count = ref(0)
  const add = () => {
    count.value++
  }

  // 主文件中需要用到 count 和 add 函数
  return {
    count,
    add
  }
}
// 向外默认导出
export default useCount
```

`useTitle.js`文件，用于处理 title 相关的逻辑：

```js
import { onMounted, onUpdated } from "vue"
// 注意此文件中需要用到 count 值 所以作为参数传入
const useTitle = (count) => {
  onMounted(() => {
    document.title = count.value
  })

  onUpdated(() => {
    document.title = count.value
  })
}
export default useTitle
```

原始的 vue 文件可以简化为：

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="add">add</button>
  </div>
</template>

<script setup>
// 导入两个文件
import useCount from './useCount'
import useTitle from './useTitle'

// 解构出 count 和 add 函数
const { count, add } = useCount()
// 传入 count 
useTitle(count)
</script>
<style scoped></style>
```

通过对模块进行拆分，实现对于文件的简化，同时可以忽略已经实现的模块文件的内容，只关注自己的目标即可

### 自定义指令 Custom Directive

vue 允许注册自定义的指令，例如，实现一个指令，用于自动聚焦：

自定义指令对象：`myDirective.js`文件：

```js
const myDirective = {
  mounted(el) {
    el.focus()
  }
}

export default myDirective
```

在 `main.js`文件中注册该自定义指令：

```js
import myDirective from './components/customize-directives/myDirective'

app.directive('focus', myDirective)
```

使用：

```vue
<input type="text" v-focus />
```

注意定义的指令名为`focus`，但是实际使用为`v-focus`

### 路由插件

安装插件：`npm i vue-router`

首先创建子组件，例如 Left.vue 和 Right.vue，创建路由配置文件`router.js`：

```js
import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router'
// 导入路由所需的组件
import Left from './Left.vue'
import Right from './Right.vue'

// 定义路由规则
const routes = [
  // 第一条路由 地址/指向根页面（不跳转）
  {
    path: '/',
    redirect: ''
  },
  // 第二条路由 地址/left指向 left 组件页面
  {
    path: '/left',
    component: Left
  },
  // 第三条路由 地址/right指向 right 组件页面
  {
    path: '/right',
    component: Right
  }
]
// 创建路由
const router = createRouter({
  routes,
  history: createWebHistory()
})

export default router
```

在`main.js`中注册路由文件：

```js
import router from './components/routers/router'

const app = createApp(App)
app.use(router)
```

在主页面中写导航链接：

```vue
<template>
  <div class="inside">
    <p>some text</p>
    <!-- router-link 用于指定跳转链接 to 指定跳转的目标地址 -->
    <router-link to="/">Main page</router-link>&nbsp;
    <router-link to="/left">LEFT</router-link>&nbsp;
    <router-link to="/right">RIGHT</router-link>
    <!-- router-view 相当于占位 跳转之后的页面内容展示在这里 -->
    <div>
      <router-view></router-view>
    </div>
  </div>
</template>
```

### Pinia

Pinia 是 Vue 的存储库，借助 Pinia 可以实现跨组件共享状态，其使用方法如下：

首先安装 Pinia：

```
npm i pinia -S
```

其次在 `main.js`中注册 pinia：

```js
import { createPinia } from 'pinia'

const app = createApp(App)
const pinia = createPinia()
app.use(pinia)
// 或者写为 app.use(createPinia())
app.mount('#app')
```

定义数据储存文件 `counterStore.js`：

```js
import { defineStore } from 'pinia'

const useCounterStore = defineStore('counterStore', {
  state() {
    return {
      count: 0
    }
  }
})

export default useCounterStore
```

在组件中使用：

```vue
<!-- Pinia 测试 主页面 -->
<template>
  <div class="main-container">
    <h3>Main page</h3>
    <p>Count: {{ counterStore.count }}</p>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
// 导入
import useCounterStore from '../../store/counterStore.js'

const counterStore = useCounterStore()
</script>
<style></style>

```

此时可以看到页面中显示 count 值

如果想要通过方法控制 count 的值，需要在 actions 节点中定义，在`counterStore.js`文件中增加：

```
  actions: {
    add() {
      this.count++
    },
    sub() {
      this.count--
    }
  }
```

同样，在`Main.vue`中调用：

```vue
<button @click="counterStore.add()">add</button>
<button @click="counterStore.sub()">sub</button>
```

#### 在不同组件中使用同一状态

在其他组件中使用同一状态或者对其进行改变用法相同：

```vue
<template>
  <div class="left-container">
    <h3>Left component</h3>
    <p>Count: {{ counterStore.count }}</p>
    <button @click="counterStore.add()">add</button>
    <button @click="counterStore.sub()">sub</button>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
    
import useCounterStore from '@/store/counterStore.js'
const counterStore = useCounterStore()
</script>
<style scoped></style>

```

#### 通过解构赋值简化写法

需要引入`storeToRefs`，：

```js
import { storeToRefs } from 'pinia'
const pstorage = useProductStorage()
const { products } = storeToRefs(pstorage)
```

注意解构赋值只能用于变量（状态），方法无法得到

#### 案例：实现购物车基本功能

**准备数据：**

将数据构建为 json 格式文件 `api.json`：

```json
{
  "data":[
    {
      "id": 1,
      "name": "Good1",
      "price": 2000,
      "inventory": 3
    },
    {
      "id": 2,
      "name": "Good2",
      "price": 3000,
      "inventory": 3
    },
    {
      "id": 3,
      "name": "Good3",
      "price": 40000,
      "inventory": 2
    }
  ]
}
```

这里模拟从服务器接口中获取数据，为了更方便的测试前端页面，数据接口可以借助 json-server 工具实现，安装以及使用如下：

- 全局安装 json-server:

  ```
  npm i json-server -g
  ```

- 按照如下格式启动一个node服务器，并提供数据接口：

  ```
  json-server .\src\components\pinia\data\api.json -p 9000
  ```

  需要提供的是数据的 json 文件，并为这个接口服务指定一个端口，此时 json-server 工具会生成一个链接：

  ```
    Resources
    http://localhost:9000/data
  ```

  打开该链接可以看到数据内容，注意使用 json-server 工具要新开启一个终端，并且在使用时保持其运行，上述链接即为模拟数据的接口

- 除了模拟从服务器接口中请求数据，json-server 也可以模拟向服务器发送数据，例如：

  ```js
  axios.post('http://localhost:9000/data', {
      id: 4,
      name: "Good4"
  })
  ```

  表示向 data 接口传入指定对象数据

为了模拟真实场景下获取数据，需要借助 Ajax 请求数据，在 vue 中，常使用其封装 Axios，首先安装 axios：

```
npm i axios -S
```

**创建商品store文件**

由于要保存商品数据，因此在 state() 下创建 `products`数组，该数组为空数组：

```js
const useProductStore = defineStore('productStore', {
  state() {
    return {
      products: []
    }
  },
})
```

在页面加载期间， products 数据应请求服务器并返回，请求数据是一个异步过程，这里使用 async-await 函数获取数据，并定义在 action 节点的 loadData 函数下：

```js
actions: {
    async loadData() {
        let results = await axios.get('http://localhost:9000/data')
        this.products = results.data
    }
}
```

最后将返回的数据同步到 products 状态中

在 `product.vue` 页面中使用 products 状态进行填充，注意页面加载数据应该在 onMounted 期间进行：

```js
onMounted(() => {
  pstorage.loadData()
})
```

**创建购物车store文件**

`carStore.js`要维护一个 cartList，用于保存不断添加的购物车商品，要实现的两个功能为：

- 添加进购物车功能：页面中点击添加商品后，从当前购物车中查找，如果之前没有添加过，则加入 cartList 中，如果之前添加过，则使其 quantity 加一，添加动作完成之后，将商品的剩余数量 inventory 减一（注意这里的 cartList 中应包含商品的所有属性，并且增加 quantity 属性用于保存数量）
- 增加计算属性，实时统计总价

首先在 state 中创建保存购物车数据的 cartList ，其数据类型为 数组：

```js
  state() {
    return {
      cartList: []
    }
  },
```

在 action 节点定义添加购物车的逻辑：

```js
  actions: {
    addToCart(product) {
      const p = this.cartList.find((value) => {
        return value.id === product.id
      })

      if (p) {
        p.quantity++
      } else {
        // 这里使用展开运算符拓展原有的 product 对象，为其增加数量属性
        this.cartList.push({
          ...product,
          quantity: 1
        })
      }
      // 减少库存
      product.inventory--
    }
  },
```

这里首先判断要添加的商品 product 是否已经存在于 cartList 中，如果存在则使其数量加一，反之则将商品对象添加进 cartList 并添加 quantity 属性，无论商品是否存在于购物车中，最终都要将库存数量减一

在 getters 节点定义计算属性：

```js
  getters: {
    totalPrice() {
      // 注意 reduce 函数的使用
      return this.cartList.reduce((sum, cl) => {
        sum += cl.price * cl.quantity
        return sum
      }, 0)
    }
  }
```

totalPrice 计算属性用于计算 cartList 中所有商品的总价值，其使用方式与 state 中的定义数据一样

### 在 vue3 配置文件中配置路径

导入 vue 组件的时候，可能会由于目录的层级关系出现多个`../../`，可以在`vite.config.js`配置文件中对路径进行设置：

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  }
})
```

此时`@`即表示从`vite.config.js`文件同级目录下的`src`目录开始算起

