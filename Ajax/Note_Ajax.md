# Ajax Note

## 客户端与服务器

- 服务器：负责存放和对外提供资源的计算机
- 客户端：负责获取和消费资源的计算机

## URL

统一资源定位符（Uniform  Resource  Locator），标识资源的唯一存放地址

组成部分：

- 通信协议
- 服务器名称
- 具体存放位置

## 客户端与服务器的通信过程

请求 - 处理 - 响应

1. 客户端：发起资源请求
2. 服务器：接收资源请求
3. 服务器：内部处理请求
4. 服务器：响应

## 资源请求方式

- get：获取服务器资源（html，css，js等等）
- post：向服务器提交数据，向服务器发送资源，登录信息、注册信息等等数据提交操作

## Ajax

Asynchronous Javascript And XML（异步JS和XML）：在网页中利用 XMLHttpRequest 对象和服务器进行数据交互的方式，用于实现网页和服务器中间的数据交互

**常见应用场景：**

- 注册用户名时动态监测用户名是否已经存在
- 搜索时动态显示相关搜索内容
- 数据分页显示：点击页码的时候，根据页码动态刷新表格数据
- 数据的增删改查

## jQuery 中的 Ajax

jQuery对浏览器中的XMLHttpRequest进行了封装，提供了一系列Ajax函数，最常使用的三个为：

- `$.get()`： 用于获取数据
- `$.post()`：用于请求数据
- `$.ajx()`：包含以上两个功能

### $.get() 方法

`$.get()`方法功能为发起 get 请求，从而将服务器上的资源请求到客户端使用：

```
$.get(url, [data], [callback])
```

其中，各项参数：

- url：数据请求的地址，必选参数
- data：请求资源时要携带的数据，可选参数
- callback：请求成功的回调函数，可选参数

例子：

```js
$.get('http://liulongbin.top:3006/api/getbooks', function(res){
    console.log(res);
});
```

带参 get 请求：

```js
// 参数以对象形式传入
$.get('http://liulongbin.top:3006/api/getbooks', {id: 1}, function(res){
    console.log(res);
});
```

### $.post()方法

`$.post()`方法用于发起 post 请求，其格式为：

```
$.post(url, [data], [callback])
```

其中三个参数：

- url： 提交数据的地址
- data：要提交的数据，注意这是可选参数
- callback：数据提交的回调函数

例子：

```js
const book = { bookname: 'book', author: 'author', publisher: 'publisher1' };
$.post('http://liulongbin.top:3006/api/addbook', book, (res) => {
    console.log(res);
});
```



### $.ajax()方法

相比于 get 和 post 方法， ajax() 方法功能更为综合，通过配置类型为 `GET` 或`POST`发送 get 或 post 请求：

```js
$.ajax({
    type: '',
    url: '',
    data: {},
    success: function(res){}
})
```

get 类型请求例子：

```js
$.ajax({
    type: 'GET',
    url: 'http://liulongbin.top:3006/api/getbooks',
    data: { id: 1 },
    success: function (res) {
        console.log(res);
    },
});
```

post 类型请求例子：

```js
$.ajax({
    type: 'POST',
    url: 'http://liulongbin.top:3006/api/addbook',
    data: { bookname: 'book', author: 'author', publisher: 'pub' },
    success: function (res) {
        console.log(res);
    },
});
```

## 接口

### 接口概念

使用 Ajax 请求数据的时候，被请求的 url 地址就叫做数据接口，每个接口必须有请求方式，例如：

```
http://liulongbin.top:3006/api/addbook （post）
```

### 接口文档

对接口的说明文档，主要包含的内容有：

- 接口名称：标识不同接口，简单说明
- 接口url：接口调用地址
- 调用方式：接口调用方式，get、post
- 参数格式：参数名称、类型、是否必选等等
- 响应格式：返回值的名称、类型等
- 返回示例



## 案例：书籍管理

### 准备

- jQuery.js
- Bootstap.css
- 安装 vscode 插件 bootstrap  3 snippets

### 注意点

- **对于动态添加的内容**，使用`ele.on('click', function(){})`方式失效，**需要使用事件委托方式进行绑定**

  ```
  $('.father-ele').on('click', '.son-ele', function(){...})
  ```

- 文本框等内容提交之前要验证合法，通过`.trim()`等方法

- 删除常通过 id 确定元素，为每个元素设定 id 的时候，类名一般约定以 `data-` 开头

## Form 表单

### 表单概念

表单用于数据的收集，由三个基本元素组成：

- 表单标签
- 表单域
- 表单按钮

### 表单属性

#### action

action规定提交表单的目标 url 地址，值为 url 地址，如果 action 为空，则数据提交到本页面

#### target

target属性指定在何处打开 url 页面，常用取值为：

- `_self`： 默认取值，在同框架中打开
- `_blank`：在新窗口中打开

#### method

规定以何种方式提交：

- get：默认，适合提交较少量的简单数据
- post：适合提交大量数据以及文件上传

**实际场景下**，表单使用 post 提交用的最多

#### enctype

用于规定发送表单数据之前如何对数据进行编码，可选值有三个：

- `application/x-wwww-form-urlencoded`：默认值，表示在发送前对所有字符进行编码
- `multipart/form-data`：不对字符进行编码，**如果涉及文件上传，必须使用该值**
- `text/plain`：空格转化为 + ，但不对特殊字符编码（很少使用）

### 表单同步提交

表单的同步提交是指点击 submit 按钮的时候，页面跳转到指定 url 页面，这样带来的问题是：

- 页面会发生跳转
- 之前页面的数据会丢失

其解决方案是：**表单只负责采集数据，使用 Ajax 进行实际的数据提交**

### 通过 Ajax 提交表单数据

#### 监听表单提交数据

使用 `submit`监听提交事件：

```js
$('#form1').on('submit', function(){
    alert('submit.');
});
```



#### 阻止表单默认提交

调用事件对象的 `event.preventDefault()`函数来阻止表单的提交页面跳转，例如：

```js
$('#form1').on('submit', function(event){
    alert('submit.');
    event.preventDefault();
});
```

#### 快速获取表单中的元素

使用`serialize()`函数快速得到表单数据：

```
<input type="text" class="form-control" id="" name="uname" placeholder="Input field">

$('#form1').serialize()
$(this).serialize()
```

**注意所有的 form 元素必须都要设置 `name` 属性**，否则无法获取值，其返回的结果形式为：

```
uname=zhangsan&upwd=pwd&gender=man
```

### 案例：评论页面

注意点：

- 文件：`5-msg-board.html(js)`

- 单独抽离 js 文件

- 在页面开始就要获取评论信息，随后添加评论之后也要重新获取最新评论信息填充页面

- 提交信息时首先使用 `preventDefault()`方法阻止默认的提交事件：

  ```
  e.preventDefault();
  ```

- 注意提交失败的时候需要返回，并阻止后续对于提交成功的判断，所以要使用 return：

  ```
  return alert('提交失败');
  ```

- 提交成功之后要清空表单中的数据，可以使用 `reset()`函数实现，但是注意该方法为原生语法，使用 jq 时候要先转化为 dom 元素再调用：

  ```
  $('#formAddCmt')[0].reset();
  ```

## 模板引擎

### 功能

模板引擎可以根据指定的**模板结构**和**数据**，自动生成完整的 html 页面，其优势：

- 减少字符串的拼接
- 使得代码结构更清晰
- 代码更易于阅读和维护

### art-template 模板引擎

#### 安装

地址：

```
https://aui.github.io/art-template/zh-cn/docs/installation.html
```

 直接将链接的 js 文件另存到本地，即可在项目中使用 `<script>` 标签进行引入

#### 使用

1. 导入 `art-template`
2. 定义数据
3. 定义模板
4. 调用  template 函数
5. 渲染页面结构

例如：

```html
<!-- 定义模板 使用html页面格式 -->
<script type="text/html" id="tpl-user">
        <h3>{{title}}</h3>
        <p>{{paragraph}}</p>
        <div>
            {{@ content}}
    </div>
</script>

<script>
    // 定义渲染数据
    var data = {title: 'test title', paragraph: 'this is a test paragraph.', content: '<h3>test title</h3>'};

    // template
    var htmlStr = template('tpl-user', data);

    // 填充渲染页面
    $('#container').html(htmlStr);
</script>
```



#### 标准语法-输出

- 原文输出`{{@ value}}`：如果 value 中包含了 HTML 标签结构，使用原文输出才能使得页面标签被正常渲染

  ```html
  <div>
      {{@ content}}
  </div>
  
  content: '<h3>test title</h3>'
  ```

- 条件输出 `{{if val1}} 输出内容1 {{else if val2}} 输出内容2 {{/if}}`：

  ```html
  <div>
      {{if flag === 1}}
      {{jug1}}  -- 如果 flag 为1 则输出 jug1
      {{else if flag === 0}}
      {{jug2}}  -- 如果 flag 为0 则输出 jug2
      {{/if}}
  </div>
  
  var data = {flag: 0,jug1: 'flag 1',jug2: 'flag 0',}            
  ```

- 循环输出 `{{each}}`语法循环数组，使用`{{$index}}`获得当前的索引，使用`{{$value}}`获得当前的循环项：

  ```html
  <ul>
      {{each listItems}}
      <li>{{$index}} --- {{$value}}</li>
      {{/each}}
  </ul>
  
  var data = {listItems: ['list1', 'list2']}
  ```

#### 过滤器

本质上是一个函数，使用格式为：

```
{{value | filterName}}
```

定义格式：

```
template.defaults.imports.filterName = function(value){xxx};
```

例如：

```html
<div>
    Date now: {{regTime | dateFormat}}
</div>

template.defaults.imports.dateFormat = function (date) {
    var y = date.getFullYear();
    var m = date.getMonth() + 1;
    var d = date.getDate();
    // 返回结果
    return y + '-' + m + '-' + d;
}
```

#### 案例-新闻

注意点：

- 显示时间的时候， 服务器返回的结果是字符串格式的，因此要先使用返回的字符串初始化日期对象，然后再进行时间的格式化
- 标签同理
- 注意处理图片地址，**服务器返回的地址是相对路径，所以要手动拼接服务器地址**

### 模板引擎原理

原理：正则表达式 



## XMLHttpRequest

XMLHttpRequest (xhr) 是浏览器提供的 js 对象，用于请求服务器上的数据，Ajax 即是基于 xhr 对象封装出来的

### 使用xhr发起 get 请求

过程：

1. 创建 xhr 对象
2. 调用 xhr.open()函数
3. 调用 xhr.send()函数
4. 监听 xhr.onreadystatechange事件

例如：

```js
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://www.liulongbin.top:3006/api/getbooks');
// 带参数的 get 请求 (查询字符串)
xhr.open('GET', 'http://www.liulongbin.top:3006/api/getbooks?id=1');
xhr.send();
xhr.onreadystatechange = function () {
    // 监听xhr对象的请求状态 和 服务器的响应状态 readyStatus 和 status 的值为固定写法
    // 并且 xhr.status 与返回的 responseText 中的status并不是同一个
    if (xhr.readyState === 4 && xhr.status === 200) {
        console.log(xhr.responseText);
    }
}
```

### readyState属性

xhr的 readyState 属性用于表示当前 Ajax 请求所处的状态，并且在请求过程中不断变化：

- 0： 表示 xhr 对象被创建
- 1：open方法已经调用
- 2：send方法已经调用，响应头已经被接收
- 3：数据接收中
- 4：ajax请求已经完成，数据传输已经彻底 **完成或者失败**

### 查询字符串

即在 url 后面添加向服务器发送信息的字符串，不同的参数使用 `&` 分隔，使用`$.ajax()`或者使用`$.get()`或者直接使用 xhr 发起 get 请求的方法，携带参数的时候，本质上都是将参数以查询字符串的方式追加到 url 后面

```
$.get('url', {name: 'a', age: 20}, function(){...})
等价于
$.get('url?name=a&age=20')
```

### url编码

url地址中，只允许出现英文相关的字母、标点符号和数字，因此中文等字符出现在url中时，必须要进行编码（使用安全字符表示不安全字符）（浏览器自动编码）

js提供了进行编码与解码的函数：

```js
var str = '你好';
var res = encodeURI(str);
console.log(res);

console.log(decodeURI(res));
```

### 使用xhr发起post请求

步骤：

1. 创建xhr对象
2. 调用 xhr.open() 函数
3. 设置 Content-Type 属性 （固定写法）
4. 调用 xhr.send() 函数，同时指定发送的数据
5. 监听 xhr.onreadystatechange 事件

例如：

```js
var xhr = new XMLHttpRequest();
xhr.open('POST', 'http://www.liulongbin.top:3006/api/addbook');
// 固定写法 并且必须位于 open  和 send 方法之间
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
// 直接使用查询字符串形式传入
xhr.send('bookname=测试书&author=测试作者&publisher=测试出版');
xhr.onreadystatechange = function(){
	if(xhr.readyState === 4 && xhr.status === 200){
        console.log(xhr.responseText);
    }
}
```

### 数据交换格式

数据交换格式是服务器与客户端之间进行数据传输交换的格式，前端常用的数据交换格式为 XML 和 JSON，其中使用最广泛的是 JSON（Javascript object notation），是 **js 的对象和数组的字符串表示方法**，即json本质为字符串，json作为一种轻量级的文本数据交换格式，比xml更小、更快、更易解析

#### json 的两种结构

**对象结构**：使用键值对结构`{"key": "value"/value}`，其中 **key 必须是英文双引号包裹的字符串**，value 可以是 数字、字符串、布尔值、null、数组和对象 六种类型（只能是这六种，不能为 undefined 或 方法等），注意如果 value 也是字符串形式，则必须也使用 双引号 包裹。

**数组结构**：使用 `[]`包裹，数据类型同上

注意点：

- 属性名必须使用双引号包裹
- 字符串类型的值必须要使用双引号包裹（json中没有单引号）
- json中不能写注释
- json的最外层必须是对象或者数组形式
- 不能使用 undefiend 或者 函数作为 json 的值

#### json 和 js 对象的关系

json 是 js 对象的字符串表示法，本质是一个字符串，可以使用 `JSON.parse()` 将 json 字符串转化为 js 对象：

```js
var jsonStr = '{"name": "zs", "gender": "man"}';
var obj = JSON.parse(jsonStr);
```

使用 `JSON.stringify()`方法可以将对象转化为 json 字符串：

```js
JSON.stringify(obj)
```

json 字符串和 js 对象之间的相互转化也是序列化和反序列化的过程：

- 数据对象转化为字符串叫做序列化 -- JSON.stringify()
- 字符串转化为数据对象叫做反序列化 -- JSON.parse()

## XMLHttpRequest Level2

传统 xhr 的问题：

- 只支持文本数据的上传，无法用来读取和上传文件
- 传送和接收数据的时候，没有进度信息，只能提示有没有完成

基于此，xhr level2 改进的功能有：

- 可以设置 http 请求的时限
- 可以使用 FormData 对象管理表单数据
- 可以上传文件
- 可以获得数据传输的进度信息

### 设置 http 请求时限

直接对 xhr 对象设置 timeout 属性，设置 ontimeout 的回调函数：

```js
xhr.timeout = 30;
xhr.ontimeout = function(){
    console.log('Time out.');
}
```

### FormData对象管理表单数据

Ajax操作往往是用于提交表单数据，H5新增的 FormData 对象可以模拟表单操作，例如：

```js
// 创建 FormData 对象
var fd = new FormData();
// fd对象添加表单项
fd.append('uname', 'zs');
fd.append('upwd', '1234');

var xhr = new XMLHttpRequest();
xhr.open('POST', 'http://www.liulongbin.top:3006/api/formdata');
// 发送数据
xhr.send(fd);
```

使用 FormData 对象也可以快速获取网页表单的值，例如：

```
var form = document.querySelector('#form1');
...
var fd = new FormData(form)
```

注意这里使用表单元素作为 FormData 对象的初始化值的时候，必须使用原生 dom 操作，如果使用 jq 的写法，注意使用`var fd = new FormData(form[0])` 进行转化

### 上传文件 

使用 xhr 实现基本的文件上传：

1. 定义UI结构，使用 `<input type='file'>`定义文件上传框
2. 上传之前首先验证是否有文件被选中，原生js中 `<input type='file'>.files`返回的是被选择的文件列表，可以通过判断该列表长度来判断是否选择了文件
3. 向 FormData 中追加文件，创建一个 FormData 对象模拟表单数据方便发送
4. 建立 xhr 对象发起文件上传的请求
5. 监听 onreadystatechange 事件， 判断文件是否已经上传成功

#### 为文件上传过程添加bootstrap进度条显示

xhr2中，通过监听 onprogress 和 onload 两个事件可以得到资源上传的进度，例如：

```js
xhr.upload.onprogress = function (e) {
    // e.lengthComputable表示当前上传的资源是否可计算
    if (e.lengthComputable) {
        // e.loaded: 已传输的字节
        // e.total: 需传输的字节
        var persentComp = Math.ceil((e.loaded / e.total) * 100);
        console.log(persentComp);
        //通过实时计算上传完成度 可以使用进度条等形式进行展示
        $('#percent').attr('style', 'width:' + persentComp + '%').html(persentComp + '% complete');
    }
}
// xhr.upload.onload 用于监听上传完成事件
xhr.upload.onload = function(){
    // 上传完成后改变进度条样式
    $('#percent').removeClass().addClass('progress-bar progrss-bar-success');
}
```

PS：测试文件上传进度的时候需要降低浏览器上传速度，可以通过调试工具中的 `Network  ` 中下拉列表中选择 `slow 3G`学则较小的传输速度，方便测试。

#### jq 文件上传及进度检测

例如：

```js
var files = $('#fileSelector')[0].files;
if(files.length <= 0){
    return alert('choose one file');
}
var fd = new FormData();
fd.append('avatar', files[0]);
console.log('ajax');
$.ajax({
    method: 'POST',
    url: 'http://www.liulongbin.top:3006/api/upload/avatar',
    data: fd,
    // 使用 FormData 默认的 content-type值
    contentType: false,
    // 不对 FormData 中的数据进行 url 编码 而是将 formData 数据原样发送到服务器
    processData: false,
    success: function(res){
        console.log(res);
    }
});
```

注意在上传文件的时候 `contentType` 和 `processData`必须设置为 false。

通过 `ajaxStart(callback)` 和 `ajaxStop(callback)`,可以实现对资源上传的监察：

```js
$('document').ajaxStart(function(){
    console.log('ajax start');
    $('#load').show();
});
$('document').ajaxStop(function(){
    console.log('ajax end');
    $('#load').hide();
});
```

注意上面两个事件会监听页面中所有的 ajax 请求，并且只能附加在 `document` 中，不能附加到 `body`或其他 dom 元素中



## Axios 

Axios 是专注网络数据传输的 js 库，是对 xhr 的封装。

### 使用Axios 发起 get 请求

```
axios.get(url, {params: {args}}).then(callback)
```

例如：

```js
$('#btnGet').on('click', function(){
    let url = urlPre + 'get';
    let person = {name: 'zs', age: 20};
    axios.get(url, {params: person}).then((res)=>{
        console.log(res);
        console.log(res.data);
    });
});
```

### 使用 Axios 发起 post 请求

```
axios.post(url, data,).then(callback)
```

例如：

```js
$('#btnPost').on('click', function(){
    let url = urlPre + 'post';
    var addr = {location: 'beijing', address: 'shunyi'};
    axios.post(url, addr).then(function(res){
        console.log(res);
        console.log(res.data);
    });
});
```

### 直接发起 get 或者 post 请求

```js
axios({
    method: xxx,
    url: xxx,
    params: xxx   ---- get使用params属性
    data: xxx   ---- post使用data属性
}).then(function(res){res.data})
```

例如：

```js
// 测试 axios 直接发起 get 请求
$('#btnAxiosGet').on('click', function(){
    let url = urlPre + 'get';
    let person = {name: 'zs', age: 20};
    axios({
        method: 'GET',
        url: url,
        params: person,
    }).then(function(res){
        console.log(res);
        console.log(res.data);
    });
});

// 测试 axios 直接发起 post 请求
$('#btnAxiosPost').on('click', function(){
    let url = urlPre + 'post';
    let person = {name: 'zs', age: 20};
    axios({
        method: 'POST',
        url: url,
        data: person,

    }).then(function(res){
        console.log(res);
        console.log(res.data);
    });
});
```

### 注意点

无论是使用 `axios.get/post`还是直接发起 `axios({get/post})`的方法，`get`方式的数据都要通过 `params`属性进行指定，而对于 `post` 属性，使用 `axios.post`可以直接写数据对象，而使用`axios({data: xxx})`方式中必须要使用`data`属性传入数据。

**与 Ajax 方式传输数据形式上的区别：**

两者形式基本一致，只是 axios 直接返回的结果中，`res.data`才是有效数据，ajax直接返回的`res`即为数据

## 同源策略和跨域

同源：**协议**、**域名**和**端口**相同的页面具有相同源

同源策略：**浏览器**提供的安全功能，规定 A 网站的 js 不允许和 非同源的网站 B之间进行资源交互，例如：网页的 cookie、localstorage、indexedDB 以及 dom 或者 发送 ajax 等

跨域：如果协议、域名、端口至少任意一个不同，则为跨域

浏览器可以发起跨域请求，只是返回的数据会被同源策略拦截

目前解决跨域的方法：

- JSONP：兼容较低版本的浏览器 但是只能使用 get 请求
  - 利用 `<script>`标签不受跨域影响
- CORS：较晚出现 不兼容较低版本浏览器

## 防抖和节流

防抖策略：事件触发后，延迟 n 秒再执行，如果 n 秒内又被触发，则重新计时（保证最终事件只执行一次），应用场景：

- 连续输入的时候，没输入一个都会发一次请求，通过防抖策略保证最终只执行一次请求

节流策略：减少一段时间内事件的触发概率，防止事件被无限次触发，应用场景：

- 鼠标连续不断的点击某个事件 --- 只在单位时间内触发一次
- 懒加载的时候监听滚动条的位置，不必每次滑动都计算，减少计算次数，降低 cpu 计算

## HTTP

通信协议：双发发送消息所规定好的格式规则（例如信封的填写规则）

对应到互联网中，客户端和服务器之间进行网页内容的传输，则通信的双发必须遵守网页内容的传输协议，网页内容（超文本），所以网页内容的传输协议又叫做超文本传输协议（Hyper Text Transfer Protocol）-- HTTP 协议

### HTTP 请求消息

HTTP请求消息（请求报文）的组成部分：

- 请求行
  - 请求方式
  - URL
  - HTTP协议版本
  - 例如`POST /api/post HTTP/1.1`
- 请求头：用于描述客户端的基本信息，例如：
  - User-Agent ： 说明当前是什么类型的浏览器
  - Content-Type：描述发送到服务器的数据格式
  - Accept：描述客户端能够接收什么类型的返回内容
  - Accept-Language：描述客户端期望接收哪种人类语言的文本内容
- 空行：分隔头部和请求体
- 请求体（只有post才有请求体）

### HTTP响应消息

组成部分：

- 状态行
  - 协议版本
  - 状态码
  - 状态码描述文本
- 响应头部：描述服务器基本信息，采用多行键值对的方式
- 空行
- 响应体：响应给客户端的资源

### HTTP 请求方法

用于表明对服务器上资源执行的方法（9种），常用的有：

- GET：查询
- POST：新增
- PUT：修改（替换旧资源）
- DELETE：删除指定资源

### 响应状态码

用于标识响应的状态，浏览器根据状态码可以知道请求结果是成功还是失败

http 状态码由三个十进制数字组成，第一个数字定义了状态码的类型，后面两个数字对状态码进行细分：

- 1**： 收到请求，需要请求者继续操作（很少使用）
- 2**：成功，操作被成功接收并且处理，常见的状态码为：
  - 200： ok 请求成功，常用于 get、post
  - 201：Created，已创建，成功请求并创建了新的资源，通常用于 post、put 请求中
- 3**：重定向，需要进一步的操作：
  - 301：moved permanently，永久移动，请求的资源已经永久移动到其他位置，浏览器会自动定位到新的 url
  - 302：Found，临时移动，客户端应继续使用原有的 URL
  - 304：Not Modified，所请求的资源未修改，服务器不会返回资源，客户端会从缓存中访问资源
- 4**：客户端错误，请求中包含语法错误或者无法完成请求
  - 400：bad request，语义有误，当前请求无法被理解或参数有误
  - 401： unauthorized，当前请求需要用户验证
  - 403：forbidden，服务器已经理解请求，但是拒绝执行
  - 404：not found，服务器无法根据客户端的请求找到资源
  - 408：request timeout，服务器等待客户端发送请求时间过长
- 5**：服务器错误，服务器在处理请求过程中出现错误
  - 500：internal server error，服务器内部错误，无法完成请求
  - 501：not implemented，服务器不支持此请求方法
  - 503：Service Unavailable，超载或者系统维护，暂时无法处理客户端请求