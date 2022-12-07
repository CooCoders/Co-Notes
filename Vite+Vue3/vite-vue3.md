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
- Vetur - Vue 构建辅助工具

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

### 模板语法

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



### 计算属性

