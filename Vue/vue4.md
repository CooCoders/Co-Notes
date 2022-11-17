# Vue 4

## ref

### ref 处理 DOM

Vue 中基本不会操作 DOM， 数据与 DOM 的关系由 MVVM 自动处理，也因此不建议使用 jQuery

如果需要处理 DOM 元素，可以使用 ref：

首先为需要操作的 DOM 上绑定 ref 属性

```
<h1 ref="myh1">Title</h1>
```

然后通过 ref 对该元素进行操作：

```
this.$refs.myh1.style.color = 'red'
```

### ref  处理组件

ref 属性同样可以绑定在组件上， 从而对子组件的属性和方法进行操作：

```
<Left ref="comLeft"></Left>
```

对 Left 组件的数据或方法操作：

```
this.$refs.comLeft.count = 0
this.$refs.comLeft.reset()
```

建议所有的 ref 都按照这种格式： `xxxRef`

###  nextTick(cb) 方法

组件的 nextTick(cb) 方法，会把回调函数推迟到下一个 DOM 周期之后执行，即等组件的 DOM 刷新之后，在执行 cb 回调函数，从而保证代码可以控制最新的 DOM 元素

例如，如果某个文本框会根据特定条件进行显示或隐藏：

```
<input type="text" v-if="inputVisible" ref="iptRef">
```

此时想要实现切换到显示的时候自动获得焦点，使用 ref 直接操作元素：

```
this.$refs.iptRef.focus()
```

此时会发现此对象为 undefined 的，这是因为 js 是单线陈的，上述代码执行的时候，页面尚未渲染结束，因此页面上此时并没有文本框，所以会出现 undefined 情况，可以使用 nextTick 进行改进：

```js
this.$nextTick(()=>{
    this.$refs.iptRef.focus()
})
```

###  数组遍历方法

#### some 方法

数组查找元素，不建议使用 forEach循环，例如：

```js
const arr = ['a', 'b', 'c', 'd']
arr.forEach((item, index)=>{
    console.log('1')
    if(item === 'c'){
        console.log(index)
        return
    }
})
```

此时会发现 forEach 不会自动跳出循环，即使使用 return 依然会遍历完整个数组

推荐使用 some 查找数组中的某个元素：

```js
arr.some((item, index)=>{
    console.log('1')
    if(item === 'c'){
        console.log(index)
        // 注意只写 return 的话 会执行到判断条件之后依然遍历完整个数组
        // return true 才会在退出条件满足之后不再继续遍历
        return true
    }
})
```

some 会在 return true 之后跳出循环，注意 return true 是固定写法

#### every 方法

使用 every 方法可以对数组的每个方法进行处理，例如，判断数组元素是否都满足某个条件：

```js
const arr1 = [
    {name: 'zs', status: true},
    {name: 'ls', status: false},
    {name: 'ww', status: true},
]
/* const res = arr1.every((item)=>{
            return item.status === true
        }) */
const res = arr1.every(item => item.status)
console.log(res)
```

### reduce 方法

对于数组：

```js
const goods = [
    { name: 'book', status: true, price: 10, count: 2 },
    { name: 'ball', status: false, price: 20, count: 3 },
    { name: 'bag', status: true, price: 30, count: 1 },
]
```

现需要求出所有 status 为 true 的元素的总价格，第一种方案为：

```js
let total = 0
goods.filter(item => item.status).forEach((item) => {
    total += item.count * item.price
})
```

上述过程可以使用 reduce 方法：

```js
// reduce((累加的结果, 当前循环项) => {}, 初始值)

const total2 = goods.filter(item => item.status).reduce((amt, item) => {
    return amt += item.price * item.count
}, 0)

// 简写形式
const total3 = goods.filter(item => item.status).reduce((amt, item) => amt += item.price * item.count, 0)
```

## 案例

### checkbox 的状态跟踪给

监听 checkbox 的状态变化的时候，不必使用 v-model 双向绑定，checkbox 本身带有 change 方法：

```
<input type=checkbox, @change="stateChange">
```

当 checkbox 的状态变化的时候，会自动触发此事件

如果要获取 checkbox 的最新状态，在定义 change 事件的时候，使用：

```js
<input type=checkbox, @change="stateChange"

stateChange(e){
    // 使用 e.target.checked 获得最新状态
    const newState = e.target.checked
    this.$emit('changeState', [this.id, newState])
}
```

### 标签属性书写规范

1. 指令
2. 属性绑定
3. 事件

### 小数转换

number.toFixed(2)，其中传入的数字表示保留的小数位数

### 嵌套较深的组件之间的信息传递

对于嵌套关系较深的组件（例：a ->b->c），可以使用 EventBus 方案传递消息

## 动态组件

### component 标签

动态组件，即动态切换显示组件，Vue 提供 component 标签实现组件的动态切换，其功能类似占位符，并且使用 is 属性改变当前组件，例如：

```vue
<component :is="compName"></component>
```

### keep-alive 保持组件状态

使用 component 标签实现组件的动态切换的时候，重新出现的组件与之前的组件并不是同一个实例对象（组件实例对象被隐藏的时候会被销毁）， 如想要保持组件的数据，可以使用 keep-alive 保持组件状态（inactive，缓存）：

```vue
<keep-alive>
    <component :is="compName"></component>
</keep-alive>
```

#### 监听组件缓存与激活

组件被缓存的时候，会自动触发组件的 deactivated 生命周期函数

组件被激活的时候，会自动触发组建的 activated 生命周期函数

例如：

```js
activated(){
    console.log('activated')
},

deactivated(){
    console.log('deactivated')
}
```

对于 keep-alive 组件，在创建的时候，先触发 created 生命周期函数，随后触发 activated 生命周期函数，随后切换激活/缓存状态的时候，分别会触发 activated 和 deactivated 生命周期函数

#### 指定特定组件被缓存

使用 keep-alive 包裹之后，所有的组件都会被缓存，如要实现只有指定的组件被缓存，需要使用 include 属性指定：

```vue
<keep-alive include="Left, Right">
    <component :is="compName"></component>
</keep-alive>
```

上述代码中，只有 Left 和 Right 组件会被缓存

除此之外，使用 exclude 也可以实现上述功能，但这两个属性不能同时使用

## 组件的注册名称与 name 名称

如果组件在声明的时候，添加了name 属性，则组件的名称就是 name 属性的值

- 组件的注册名称主要用于以标签的形式，将注册好的组件添加到页面结构中
- 组件的 name 名称主要用于结合 keep-alive 标签实现缓存功能，以及在调试工具中看到组件的 name 名称

## 插槽 slot

### 概念

插槽是 vue 为组件的封装者提供的能力，允许开发者在封装组件的时候，把不确定的、希望用户来指定的部分定义为插槽。例如在组件 Left 中，使用：

```vue
<template>
  <div class="left-container">
    <h3>Left 组件</h3>
    <!-- slot 占位 -->
    <slot></slot>
  </div>
</template>
```

slot 标签位置相当于定义了一个占位符，在使用 Left 组件的时候，可以传入标签等内容：

```vue
<template>
  <div class="app-container">
    <h1>App 根组件</h1>
    <hr />

    <div class="box">
      <!-- 渲染 Left 组件和 Right 组件 -->
      <Left>
        <!-- 代替 slot 的部分 -->
        <p>test for slot</p>
      </Left>
    </div>
  </div>
</template>
```

### v-slot 指令

vue 规定每个 slot 都要定义一个 name 名称，如果省略了 name 属性，则默认为 default，实际使用组件的时候，内容会默认被填充到 default 的插槽中

如想要将内容匹配到特定的 slot 中，可以使用 v-slot 指令，注意 v-slot 指令只能用于组件或者`<template>`标签中：

```vue
<Left>
    <!-- 注意格式为 v-slot:slotName 并且v-slot必须用于template标签上 -->
    <template v-slot:test1>
		<p>test for slot</p>
    </template>
</Left>
```

其简写形式为 `#`:

```vue
<Left>
    <template #test1>
		<p>test for slot</p>
    </template>
</Left>
```

此外，在组件的 slot 标签之间加入的内容为默认内容（后备内容），如果用户没有指定 template ，则会显示默认内容，指定 template 则会显示用户指定内容

### 具名插槽

定义组件的时候，如果希望预留多个 slot 位置（例如 vant 中的 nav 组件），必须要指定 name 属性，例如：

定义组件：

```vue
<template>
<div class="test-container">
    <!-- header 部分 -->
    <div class="header-box">
        <!-- 预留 slot 并指定 name 属性 -->
        <slot name="title"></slot>
    </div>

    <!-- content 部分 -->
    <div class="content-box">
        <slot name="content"></slot>
    </div>
    <!-- footer 部分 -->
    <div class="footer-box">
        <slot name="footer"></slot>
    </div>
    </div>
</template>
```

使用组建的时候匹配 slot

```vue
<Test>
    <template #title>
<p>header</p>
    </template>

    <template #content>
<p>content</p>
    </template>

    <template #footer>
<p>footer</p>
    </template>
</Test>
```

### 作用域插槽

作用域插槽，即封装组件的时候，为 slot 提供预留的数据，例如：

```vue
<div class="content-box">
    <slot name="content" msg="this is content" text="content box"></slot>
</div>
```

这里向插槽中绑定了两个属性 msg 和 text，这两个属性可以在组件使用的时候进行接收使用：

```vue
<!-- 使用 = 接收一个对象 该对象的属性即为 slot 预留的数据 -->
<template #content="obj">
    <p>content</p>
    <p>{{ obj }}</p>
</template>
```

此时打印 obj，结果为：

```
{ "msg": "this is content", "text": "content box" }
```

#### 作用域插槽的解构赋值

作用域插槽提供数据可以通过解构赋值方便取值，例如，

组件定义：

```vue
<slot name="title" val="test title" :user="userInfo"></slot>


data() {
    return {
        userInfo:{
            name: 'zs',
            age: 21
        }
    };
},
```

该插槽中定义了两个属性 val 和 user ，其中 user 返回的是一个对象

组件实例：

```vue
<!-- 通过解构赋值获取数据 -->
<template #title="{val, user}">
    <p>header</p>
    <p>{{ val }}</p>
    <p>{{ user.name }}</p>
</template>
```

## 自定义指令

### 概念

vue 除了提供的 v-model，v-for等指令外， 还支持开发者自定义的指令，自定义指令分为私有自定义指令和全局自定义指令

### 私有自定义指令

可在 directives 节点上定义并配置一个自定义指令：

```js
export default {
  // 私有自定义指令的节点
  directives:{
    // 定义名为 color 的指令 （使用时使用格式为 v-color ）
    color:{
      // 指令被绑定到元素上的时候触发 bind 函数
      // 形参 el 表示当前指令绑定到的那个 DOM 对象
      bind(el){
        el.style.color='red'
      }
    }
  }
}
```

使用该指令（注意定义的时候名字为 color，但是实际绑定到元素的时候要使用 v-color）：

```vue
<!-- 使用私有自定义指令 color -->
<h1 v-color>App 根组件</h1>
```

为自定义指令传值，可以使用 binding 参数，例如

定义指令：

```js
directives: {
    color: {
        // 为自定义指令传值需要使用 binding 参数
        bind(el, binding) {
            console.log(binding)
            el.style.color = binding.value
        }
    }
}
```

打印 binding 参数，其结果为一个对象：

```
{name: 'color', rawName: 'v-color', value: 'green', expression: "'green'", modifiers: {…}, …}
```

其中 value 属性即为接收到的数据，使用该指令时：

```
<h1 v-color="'green'">App 根组件</h1>
```

**注意这里传入必须要用单引号括起来，这样表述为js表达式**，否则，表示寻找名称为 green 的变量：

```js
<p v-color="color">some test text</p>

...

data(){
    return{
        color: 'blue'
    }
},
```

#### update 函数

bind() 函数只会在初次使用该指令时触发一次，如果需要根据 DOM 更新重新触发指令的改变，需要使用 update 函数：

```js
update(el, binding){
	el.style.color = binding.value
}
```

此时如果 binding.val 的值发生变化，该元素的颜色也会随之更新

注意 不能只定义 update 函数而不定义 bind 函数

#### 函数简写

如果 bind 和 update 函数的内容是一样的，因此可以对其进行简写：

```js
color:function(el, binding){
	el.style.color = binding.value
}

// 进一步简写
color(el, binding){
    el.style.color = binding.value
}
```

### 全局自定义指令

在某个组件的 directives 节点下定义的指令只在当前组件内生效，如果要定义全局范围内有效的指令，需要在 main.js 文件内定义：

```js
Vue.directive('color', {
  bind(el, binding) {
    el.style.color = binding.value
  },

  update(el, binding) {
    el.style.color = binding.value
  }
})

// 简写形式
Vue.directive('color', function(el, binding){
  el.style.color = binding.value
})
```

在 main.js 中：

```
Vue.config.productionTip = false
```

会给出一个提示 tip，提醒开发者在发布产品的时候要打开 production 模式

## ESLint

### 概念

用于检测代码格式规范的工具：

> http://eslint.cn/

编辑器设置：

- 关于代码缩进，为了保证代码嵌套过多的时候代码美观，可以设置 tab 缩进为两个空格（Vscode 打开设置，查找 tabsize）
- 设置 Vscode 在保存时自动格式化代码：设置里搜 format，在 editor 里勾选 Format On Save

创建安装 ESLint 的 Vue 项目：

- 在工程目录下打开 cli， 执行 vue create -PROJNAME
- 选中 Linter / Formatter
- 选择 ESLint 的语法规范（选择 ESLint + standard config）
- 选择在何时进行代码规范检查：
  - Lint on save： 保存时检查 （选择）
  - Lint and fix on commit：提交代码时检查

此时打开项目会发现较之前的项目多出了一个`.eslintrc.js`文件，该文件内的 rules 节点上有两条默认规则：

```
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off'
  }
```

no-console 规定了在生产模式下出现 console 的话会报出 warn，no-debugger 规定生产模式下出现 debugger 的话报 warn。

简单使用：例如在代码中加入额外的多个空行，保存后会出现如下格式的错误：

```
8:1  error  More than 1 blank line not allowed  no-multiple-empty-lines
```

`no-multiple-empty-lines`表示出现的错误

### 查找规则

如果出现不了解的错误，可以在 ESLint 规则网站上查看：

> http://eslint.cn/docs/rules/

例如常见的规则：

- `eol-last`：末尾必须有且仅有一行空行
- `no-trailing-spaces`：代码不应出现多余的空格
- `quotes`：默认情况下使用单引号
- `key-spacing`：对象的属性和值之间，默认有一个空格分隔
- spaced-comment：在注释中`//`或者`/*`后强制使用一致的间距
- `indent`：强制一致的缩进
- `import/first`：import导入模块语句必须声明在文件的顶部
- `no-unused-vars`：未使用的变量

如果想要修改部分规则，可以修改配置文件，首先根据要修改的规则到官网查看规则配置文档，文档中会说明可修改的内容，随后可以 根据自己的需要修改相关的配置，例如该规则报 error 还是 warn，以及检查该规则 always 还是不检查该规则 never。

修改规则配置样例：

```
rules: {
	// 默认情况下，ESLint 规定：方法的形参之前必须保留一个空格
	// 'space-before-function-paren': ['error', 'always']

	// 如果指定了规则的值为 never，则不允许在方法的形参之前保留任何空格
	'space-before-function-paren': ['error', 'never']
}
```

修改使用多单词命名组件：

```
'vue/multi-word-component-names': 'off'
```

### 配置 VSCode 按照 ESLint 格式化代码

首先安装两个插件：

- ESLint
- prettier - Code formatter

随后进行配置，对于 prettier，需要在路径：`C:\Users\NAME`下创建文件`.prettierrc`文件，其内容为：

```
{
    "semi": false,
    "singleQuote": true,
    "bracketSpacing": true
}
```

随后在 VSCode 的配置文件中设置，最近所有需要配置的内容为：

```
{
    // 这里是刚才.prettierrc文件的配置路径
    "prettier.configPath": "C:\\Users\\NAME\\.prettierrc",
    "eslint.alwaysShowStatus": true,
    "prettier.trailingComma": "none",
    "prettier.semi": false,
    // 换行
    "prettier.printWidth": 300,
    "prettier.singleQuote": true,
    "prettier.arrowParens": "avoid",
    // 设置vue文件中 html代码的格式化插件
    "vetur.format.defaultFormatter.html": "js-beautify-html",
    "vetur.ignoreProjectWarning": true,
    "vetur.format.defaultFormatterOptions": {
        "prettier": {
            "trailingComma": "none",
            "semi": false,
            "singleQuote": true,
            "arrowParens": "avoid",
            "printWidth": 300
        },
        "js-beautify-html": {
            "wrap_attributes": false
        },
    },
    "editor.codeActionsOnSave": {
        "source.fixAll": true,
    },
    "path-autocomplete.extensionOnImport": true,
    "path-autocomplete.pathMappings": {
        "@": "${folder}/src"
    },
    "workbench.colorTheme": "Default Dark+",
    "editor.fontSize": 16,
    "editor.fontFamily": "Cousine, 'Courier New', monospace",
    "liveServer.settings.CustomBrowser": "chrome",
    "open-in-browser.default": "Chrome",
    "remote.SSH.remotePlatform": {
        "test": "linux"
    },
    "liveServer.settings.AdvanceCustomBrowserCmdLine": "",
    "[html]": {
        "editor.defaultFormatter": "vscode.html-language-features"
    },
    "editor.tabSize": 2,
    "editor.formatOnSave": true
}
```

对于 js 文件，右键使用...格式化文档，如果有多个格式化程序，需要选择使用 prettier-code formatter 进行格式化，如果没有使用该程序，可以点击最后一项修改为 prettier-code formatter（Vue 文件同理）

## vue-axios

### 安装

在 vue 中使用 axios，安装：

```
npm install axios -S
```

如果出现 npm 版本冲突，使用命令：

```
npm i axios -S --legacy-peer-deps
```

### 使用

导入 axios：

```
import axios from 'axios'
```

在方法中定义：

```js
methods: {
    async postInfo() {
        const { data: res } = await axios.post('http://www.liulongbin.top:3006/api/post', { name: 'zs', age: 12 })
        console.log(res)
    }
}
```

axios 请求一般都会搭配 async-await 方法来使用，直接调用的方式问题在于如果多个组件中都要发起 axios 请求，则需要重复写多次请求路径等内容，太过繁琐

### 改进使用方法 - 将 axios 挂载到原型 & 配置请求根路径

首先在 `main.js`中导入 axios：

```
import axios from 'axios'
```

配置请求根路径：

```
axios.defaults.baseURL = 'http://www.liulongbin.top:3006'
```

挂载到 Vue 原型：（**这里使用`$http`，是通用写法，实际上起什么名字都可以**）

```
Vue.prototype.$http = axios
```

调用 axios：

```js
methods: {
    async getBookInfo() {
        const { data: res } = await this.$http.get('/api/get')
        console.log(res)
    }
}
```

**该方法的缺点：**

不利于 api 接口的复用