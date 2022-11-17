# vue 语法

## 基础概念

Vue： 一套用于构建用户界面的前端框架

- 构建用户界面：
  - 使用 vue 向 html 页面填充数据
- 框架：
  - 一套现成的解决方案，使用者必须遵守框架的规范编写业务功能
  - 学习 vue 也就是学习使用规则

### 特性

- 数据驱动视图 （数据作为核心）
  - Vue 会自动监听数据的变换，从而自动渲染页面解构
  - 数据驱动视图是单向的数据绑定
    <img src="./vue2.assets/image-20220922142159399.png" alt="image-20220922142159399" style="zoom:67%; margin=0 auto" />
- 双向数据绑定
  - 网页中表单负责采集数据，Ajax 负责提交数据
  - 填写表单的时候，双向数据绑定可以辅助开发者在不操作DOM的前提下， 自动把用户填写的内容同步到数据源中
    <img src="./vue2.assets/image-20220922142125201.png" alt="image-20220922142125201" style="zoom:67%;" />

### MVVM

MVVM是 Vue 实现数据驱动视图和双向数据绑定的核心原理，MVVM 指的是 Model、View 和 ViewModel，它把 HTML 页面拆分成了三个部分：

- Model 表示当前页面渲染时所依赖的数据源
- View 表示当前页面所渲染的 DOM 结构
- ViewModel 表示 Vue 的实例，这是 MVVM 的核心 

<img src="./vue2.assets/image-20220922143204479.png" alt="image-20220922143204479" style="zoom:67%;" />

## 基本使用

### 例子

```html
<div id="app">{{ username }}</div>

<script>
    // 创建 vue 实例
    const vm = new Vue({   // 整个 Vue 实例表示 VM
        // el 属性是固定写法 表示 vm 实例要控制页面上的哪个区域，接收的值是一个选择器
        el: '#app',        // view
        // data对象就是要渲染到页面上的数据
        data: {                     // model
            username: 'zhangsan',
        }
    })
</script>
```

### 指令

指令：（directives）是vue为开发者提供的模板语法，用于辅助开发者渲染页面的基本结构

指令的分类：

- 内容渲染指令
- 属性绑定指令
- 事件绑定指令
- 双向绑定指令
- 条件渲染指令
- 列表渲染指令

#### 内容渲染指令

内容渲染指令用于辅助开发者渲染 DOM 元素的文本内容，常用指令有：

- v-text：会覆盖调原来元素的文本
- 插值表达式{{}} Mustache 相当于占位符  （只能用在内容节点，不能用在属性节点）
- v-html： 可以渲染 html

例如：

```html
<div id="app">
    <!-- v-text指令 -->
    <p v-text="username"></p>

    <!-- 插值表达式{{}} -->
    <p>gender:{{gender}}</p>

    <!-- 使用v-html -->
    <div v-html="info"></div>
</div>

<script>
    // 创建 vue 实例
    const vm = new Vue({
        // el 属性是固定写法 表示 vm 实例要控制页面上的哪个区域，接收的值是一个选择器
        el: '#app',
        // data对象就是要渲染到页面上的数据
        data: {
            username: 'zhangsan',
            gender: 'woman',
            info: '<h4 style="color: red; fotn-weight: bold;">Vue.js</h4>'
        }
    })
</script>
```

#### 属性绑定指令

如果需要对元素的**属性**动态赋值，则需要使用属性绑定指令 v-bind：

```html
<div id="app">
    <!-- v-bind可以为属性动态绑定值 使用形式为 v-bind:attr -->
    <input type="text" v-bind:placeholder="tips">

    <!-- 简写形式 简写为: -->
    <img :src="logo" alt="">
</div>

<script>
    const vm = new Vue({
        el: '#app',
        data:{
            username: 'zhangsan',
            tips: 'place input some text',
            logo: 'https://xxxx.png'
        }
    })
</script>
```

#### 模板渲染中使用 js 表达式

```
// 算术运算
{{number + 1}}
// 三目表达式
{{ok ? 'YES' : 'NO'}}
// 调用函数
{{message.split('').reverse().join('')}}

// 属性
<div :id="'list-' + id"></div>
```

注意属性绑定内容需要动态拼接的话，字符串外面需要使用单引号包裹

#### 事件绑定指令

vue 提供了 v-on 事件绑定指令（简写形式为@），用于辅助 DOM 元素绑定事件监听，语法为：

```html
<div id="app">
    <div>{{count}}</div>
    <!-- 调用事件处理函数的格式 -->
    <button v-on:click="add">click</button>
    
    <!-- 简写形式 -->
    <button @click="add">click</button>
</div>

<script>
    const vm = new Vue({
        el: '#app',
        data: {
            count: 2,
        },
        methods: {
            // 可以简写为 add(){}  --- 推荐写法
            add: function () {
                console.log('test')
            }
        }
    })
</script>
```

原生 dom 中的 onclick， oninput以及onkeyup等事件都可以替换为 v-on:input​ 或者 @input

#### 通过this绑定数据

```js
vm.count += 1
this.count += 1
```

例如：

```js
methods: {
    add: function () {
        this.count += 1
    },
        sub(){
            vm.count -= 1
        }
}
```

vm 和 this 完全一样（指向同一块内存地址），更推荐使用 this 

#### 绑定事件传递参数

在绑定事件处理函数的时候，可以使用（）传递参数：

```
<button v-on:click="add(4)">add</button>
...

add(n){
	this.count += n
}
```

#### 事件对象

如果绑定事件的时候不传递参数：

```
<button @click="add">add</button>
```

此时事件处理函数可以打印出事件对象：

```js
add(e){
    console.log(e)  // PointerEvent
    this.count += 1
}
```

如果 add 函数传递了参数 `add(e)`， 则事件对象本身就会被覆盖，此时如果想要同时传递参数和获得事件对象，可以使用 `$event`（固定写法）：

```js
<button @click="add(1, $event)">add</button>
...
add(n, e) {
    console.log(e)
    this.count += n
    if (this.count % 2 == 0) {
        e.target.style.backgroundColor = 'red'
    }
    else {
        e.target.style.backgroundColor = ''
    }
}
```

#### 事件修饰符

在事件处理函数中调用 event.preventDefault() 或者 event.stopPropagation() 阻止默认事件和阻止冒泡是常见的需求，vue 提供了事件修饰符简化书写方式，常用的 5 个事件修饰符如下：

- prevent： preventDefault()，阻止默认事件（链接跳转、表单提交等）
- stop：stopPropagation()，阻止冒泡
- capture：以捕获模式触发当前事件处理函数
- once：绑定的事件只触发一次
- self：只有当 event.target 是当前元素自身时触发事件处理函数

例如，阻止冒泡的传统写法：

```js
<div style="height: 50px; width: 50px; background-color:aqua" @click="divHandler">
    <button @click="btnHandler">click</button>
</div>
...

btnHandler(e){
    e.stopPropagation()
    console.log('button')
}
```

使用事件修饰符：

```js
<div style="height: 50px; width: 50px; background-color:aqua" @click="divHandler">
	<button @click.stop="btnHandler">click</button>
</div>
...

btnHandler(e){
    // e.stopPropagation()
    console.log('button')
}
```

#### 按键修饰符

需要判断详细按键时，可以为键盘相关的事件添加按键修饰符，常使用的有 enter键和 esc键，例如：

```html
<div id="app">
    <input type="text" @keyup.enter="submit">

    <input type="text" @keyup.esc="clear">
</div>
```

只有按对应按键的时候才会触发事件：

```js
methods:{
    submit(e){
        console.log('submit')
        console.log('submit: ' + e.target.value)
    },
        clear(e){
            // 事件方法没有绑定参数的时候 可以配合事件对象使用
            console.log('clear')
            e.target.value = ""
        }
}
```

#### 双向数据绑定指令

v-model 双向数据绑定指令，可以在不操作 DOM 的前提下，快速获取表单的数据，例如：

```html
<div id="app">
    <input type="text" :value="username">
    <br>
    <input type="text" v-model="username">
    <br>
    <select name="" id="" v-model="city">
        <option value="">choose</option>
        <option value="1">Beijing</option>
        <option value="2">Jinan</option>
        <option value="3">Shanghai</option>
    </select>
</div>


<script>
    const vm = new Vue({
        el: '#app',
        data:{
            username: 'zhangsan',
            city: ''
        }
    })
</script>
```

使用 :value 进行属性绑定的话只是从数据源向页面渲染，而使用 v-model ，input 文本框中值改变的话，数据源也会发生变化。可以使用 v-model 的元素有：

- input 输入框 （type 可以是 radio、checkbox等等）
- textarea
- select

##### v-model 的修饰符

- number： 自动将输入值转化为数值类型
- trim：自动过滤用户输入的首尾空白字符
- lazy：输入完成之后才会更新

使用案例：

```html
<div id="app">
    <input type="text" v-model.number="n1"> + <input type="text" v-model.number="n2"> = <span>{{ n1 + n2 }}</span>
    <br>  
    <input type="text" v-model.trim="username">
    <br>
    <input type="text" v-model.lazy="address">
</div>

<script>
    const vm = new Vue({
        el: '#app',
        data:{
            n1: 1,
            n2: 2,
            username: '',
            address: ''
        }
    })
</script>
```

#### 条件渲染指令

条件渲染指令用于辅助开发者**按需控制 DOM 的显示与隐藏**，条件渲染指令有 v-if 和 v-show ，例如：

```html
<div id="app">
    <p v-if="flag">v-if</p>
    <p v-show="flag">v-show</p>
</div>
```

如果 flag 值为 false 的话，绑定的元素会隐藏，反之显示，两个指令的区别在于：

- v-if 原理：动态添加或者删除元素（**更推荐使用 v-if**）
  - 适合一开始不需要展示元素的场景而且很有可能以后不被展示
- v-show 原理：修改样式属性 display 在 none 和 block 之间切换
  - 适合切换比较频繁的情况，消耗资源更小

v-if 指令可以配合 v-if 指令一起使用，否则不会被识别，其功能与 if-else 块一样：

```html
<div v-if="type=='A'">AAA</div>
<div v-else-if="type=='B'">BBB</div>
<div v-else>CCC</div>
```

实际环境中， v-if 和 v-else 指令能处理大部分情况（判断 true 或 false），v-else-if 出现的很少

#### 循环渲染指令

v-for 指令用于基于数组循环渲染一个类列表结构，该指令需要使用 item in items 形式的语法，其中 item 为每一项，items 是待循环的数组，例如：

```js
<div id="app">
    <ul>
        <li v-for="item in user">{{item.id}} --- {{item.name}} --- {{item.age}}</li>
    </ul>
</div>

data: {
    user: [
        { id: 1, name: 'zs', age: 13 },
        { id: 2, name: 'ls', age: 16 },
    ]
}
```

列表结构：

```html
<tr v-for="item in user">
    <td>{{item.id}}</td>
    <td>{{item.name}}</td>
    <td>{{item.age}}</td>
</tr>
```

注意，v-for 指令还支持索引参数，形式为 (item, index) in items

在使用 v-for 指令的时候，官方推荐动态绑定 key：

```html
<tr v-for="(item, index) in user" :key="item.id">
    <td>{{ index }}</td>
    <td>{{ item.name }}</td>
    <td>{{ item.age }}</td>
</tr>
```

在普通 html 文件中，不绑定 key 不会出错，但是在 vue 文件中会报错，因此推荐所有文件统一添加，key 属性有几个规则：

- key 的值只能是 字符串 或者 数字
- key 的值具有**唯一性**
- 建议使用数据的 id  作为 key 值
- 指定 key 值可以提升性能，防止列表紊乱
- **注意不要使用 index 作为 key 的值， index 并不具有唯一性，并且 key 的意义在于唯一的 key 与唯一的数据项是对应的，有些情况下新数据会从头添加，此时 index 破坏了 key 与数据项之间对应关系**