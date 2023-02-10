# Node.js Note

## fs 文件读写

### readFile(filename, [option], callback)

```js
const fs = require('fs')

fs.readFile('./text.txt', 'utf-8', function(err, datastr){
    if(err){
        console.log('Read fail. ' + err.message);
    }else{
        console.log(datastr);
    }
})
```

与之类似的函数为 `readFileSync(filename, 'utf-8', callback)`，表示同步方式读取文件

### writeFile(filename, data, [option], callback)

写入成功 err 为 null， 写入失败则返回一个错误对象

**注意：**上述两个方法都是异步函数，如先写入文件再读文件，由于异步执行方式，会发现读出内容与写入内容不同，如需按照阻塞方式实现上述过程，需要使用两个方法对应的同步函数：writeFileSync 和 readFileSync，该函数按同步方式运行，在写入文件完成之前不会读文件

### 文件路径拼接

使用node命令会在默认执行位置后拼接相对路径

使用  `__dirname`得到当前文件所在的目录，然后拼接读取文件的位置，以此解决路径错误

**注意：** 上述方式只有在 CommonJs 类型下使用，如使用 ES6 方式，需要自己定义：

```js
import { URL } from 'url'

const __filename = new URL('', import.meta.url).pathname
const __dirname = new URL('', import.meta.url).pathname
```



## path 路径模块

使用 path.join() 拼接路径

```js
path = require('path');


const tmp = path.join('a', '../b', 'c');
// b\c ../相当于回到上一级（抵消上一个目录）
console.log(tmp);
```

使用 path.basename() 得到路径中文件名

```js
path.basename(fpath, ['.html'])  // 是否移除扩展名

const tmpPath = './a/c/base.file';

console.log(path.basename(tmpPath));  // base.file
console.log(path.basename(tmpPath, '.file'));  //base
```

同样，得到文件的扩展名 ： `path.extname(fpath)`

使用路径解析同样可以得到上述信息：

```
path.parse(__dirname)

{
  root: '/',
  dir: '/C:/Git/Co-Learning/Node.js/review-node',
  base: 'async-file.js',
  ext: '.js',
  name: 'async-file'
}
```



## http 模块

http模块是由 node 官方提供的，用于创建 web 服务器的模块，通过调用 http.createServer() 方法，可以将电脑变成web服务器，从而对外发布web服务

服务器与普通pc的区别在于，服务器上安装了例如 IIS ，Apache等 web 服务器软件 （phpstudy），node js可以通过 http 模块通过代码手写服务器，测试使用的地址为127.0.0.1，对应的域名为 localhost

ip -- 域名（通过DNS进行对应）-- 端口号（每个web服务占用单独一个，80端口号可以省略）

### 创建web服务器

创建步骤：

```js
// 引入 http 模块
const http = require('http');

// 创建 web 服务器实例
const server = http.createServer();

// 使用实例的 .on() 方法 为服务器绑定一个 request 事件
server.on('request', (req, res) =>{
    // 如果有来自客户端的请求 就会触发 request 事件
    console.log('Someone visit our web server.');
});

// 调用 listen 方法 启动 web服务器
server.listen(80, () =>{
    console.log('http server running at http://127.0.0.1:80');
});
```



### req 请求对象

服务器接收到客户端的请求后，会调用 server.on() 为服务器绑定 request 事件处理函数，可以通过 req 对象访问客户端相关的数据与属性：

```js
server.on('request', req =>{
    const url = req.url;
    const method = req.method;
    const str = 'Your request url is ' + req.url + ', and req method is '+req.method;

    console.log(str);
}); 
```



### res响应对象

响应对象 res 中包含了服务器相关的数据和属性，使用：

```js
server.on('request', (req, res) =>{
    const url = req.url;
    const method = req.method;
    const str = 'Your request url is ' + req.url + ', and req method is '+req.method;

    console.log(str);

    // res 是响应对象 包含了与服务器相关的数据和属性
    // 向客户端发送指定内容 并结束这次请求
    res.end(str);
}); 
```



### 解决中文乱码问题

设置响应头，改变编码格式：

```js
res.setHeadet('Content-Type', 'text/html; charset=utf-8')
```



### 根据请求内容返回不同内容

根据 url 进行判断：

```js
server.on('request', function(req, res){
    const url = req.url;
    let content = '<h1>404 not found </h1>';
    // 根据 url 判断 设置不同内容
    if(url === '/' || url === '/index.html'){
        content = '<h1>首页</h1>';
    }else if(url === '/about.html'){
        content = '<h1>关于页面</h1>';
    }
    
    res.setHeader('Content-Type', 'text/html; charset=utf-8');
    res.end(content);
});

server.listen(80, () => {
    console.log('Running at http://127.0.0.1');
})
```



## 模块化

### 模块分类

- 内置模块 （fs、http、path等等）
- 自定义模块（自行创建的每个 js 文件）
- 第三方模块（使用前需下载）



### 模块作用域

模块内定义的变量 无法在模块外使用 --- 防止全局作用域污染



### 模块共享

使用 module 对象，每个模块都有 module 对象，保存的是当前模块的一些属性，使用 module.exports 对象可以向外提供信息， 使用 require 导入模块的时候，所得的结果即是 module.exports 对象

### exports 对象

exports 对象 --- 简化写法的 module.exports 对象（省略 module 前缀）

**注意，如果页面中同时存在对 exports 和 module.exports 的赋值， 那么结果会以 module.exports 为准**，因此不要再一个文件中同时使用 module.exports  和 exports， 如果使用对象的方式设置 exports 则一定要使用 module.exports:

```
module.exports = {
    dateFormat,
}
```



## npm

npm：node.js提供的包管理工具

### moment 使用

1. 下载对应的包 `npm i[nstall] package1 package2`

   1. 下载指定版本的包：`npm i package@2.22.2`
   2. 版本号为三个数字，分别表示  大版本-功能版本-bug修复版本
   3. 卸载命令为 uninstall

2. 导入 `require('moment')`

3. 使用：

   ```js
   const moment = require('moment');
   
   console.log(moment());  // Moment<2022-09-15T16:00:48+08:00>
   
   console.log(moment().format('YYYY-MM-DD HH:mm:ss'));  //2022-09-15 16:01:34
   ```

   

### 创建package.json文件

在新项目中使用命令：

```
npm init -y
```

注意目录名必须为英文，并且无空格，使用 npm install 安装包的时候，包管理工具会自动将包的信息记录到 package.json 中

这样直接使用 `npm install` 可以直接根据 package.json 文件中的 dependencies 将全部的包都安装完

### dependencies 和devDependencies

仅在开发阶段使用的包记录在 devDependencies 中，使用命令：`npm i package -D`，可以在 npmjs 网站中查看对应安装命令

### npm 镜像服务器

切换国内源：

1. `npm config get registry`：查看当前的镜像源
2. `npm config set registry=http://....`

**借助 nrm 工具切换镜像源：**

1. `npm i nrm -g` 
2. `nrm ls` 查看所有可用源
3. `nrm use taobao`

### i5ting_toc md文件转H5

`npm install -g i5ting_toc` 全局安装

`i5ting_toc -f 文件路径 -o`



### 包的分类

- 项目包：项目所用的包
- 全局包： 工具性质的包一般安装为全局，可以通过官方文档查看是否需要全局安装才能使用

### 规范包结构

要求：

- 包必须要以单独目录存在
- 包的顶级目录下必须包含 package.json 文件
- package.json 中必须包含 name、version、main三个属性

## 模块加载

- 优先从缓存中加载
- 优先内置模块
- 自定义模块必须要有 `.\` `..\`标识为自定义模块，否则会按照内置模块或第三方模块方式加载



## Express

Express 是基于 Node平台的 web 开发框架，其三大核心功能为：

- 托管静态资源：实现静态服务器功能
- 路由
  - 路由功能：请求路径与功能的映射
  - 链式语法支持
- 中间件

功能：

- 创建 Web网站服务器 -- 提供网站服务
- 创建 API接口服务器  -- 提供 api 接口

例子：

```js
const express = require('express');

// 创建server
const app = express();

// 定义路由
// 参数1 客户端的请求 url 地址
// req：请求对象 -- 请求相关的属性和方法
// res：响应对象  -- 响应相关的属性和方法
app.get('/user', (req, res) =>{
    // 响应一个 JSON 对象
    res.send({name: 'zs', age: 13, gender:'man'});
});

app.post('/user', (req, res) => {
    //响应文本
    res.send('query success.');
});

// 监听函数
app.listen(80, ()=>{
    console.log('express server running at http:127.0.0.1');
});
```

### 接收 ajax-post 方式传入的数据

使用 ajax post 方式提交数据：

```js
$.ajax({
    type: 'POST',
    url: 'http://127.0.0.1:80/paras',
    data: { name: 'sss', age: 23 },
    success: (val) => {
        console.log(val)
    }
})
```

此时直接使用 `req.body`取得数据为空数据（未配置解析中间件 打印为 undefine ），需要首先配置中间件 body-parser：

```
const bodyParser = require('body-parser')

app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
```

直接打印 `req.body`：

```
[Object: null prototype] { name: 'sss', age: '23' }
```

可以通过 JSON 转化为字符串：

```
JSON.stringify(req.body)
```

结果为：

```
{"name":"sss","age":"23"}
```

### req.body, req.params, req.query

req.body 用于接收 post 方法的参数

req.query 接收 get 方法传递的参数，直接获取地址栏的参数

req.params 用于接收请求地址`/`后面的参数（动态参数），例如：

```js
app.get('/testParams/:name', (req, res) => {
  console.log(req.params['name'])  // taylor
  res.send('req.params test')
})
```

访问：

```
127.0.0.1:80/testParams/taylor
```



### 1. 托管静态资源

通过 express.static() 可以创建一个静态资源服务器，例如：

```
app.use(express.static('public'))
```

表示 **public目录下的资源** 对外公开访问，但是这个目录名本身不会出现在 URL 中，例如 public 文件夹下有 images 文件夹，则访问的路径为：`localhost:80/images/bg.jpg`

**Tip：这里要注意运行 server 的位置，public 文件夹必须与 server.js 文件在同一级**

托管多个静态资源目录，可以多次调用 static 函数，如果有重名的资源，则会自上而下查找

```
app.use(express.static('public'))
app.use(express.static('files'))
```

如果想要将静态资源文件夹也加入路径，可以挂在路径前缀：

```
app.use('/public', express.static('./public'))
```

此时如果想要加载静态资源，需要加入前缀 'public' **(注意前缀中不要有`./`)**

## nodemon 工具

使用 nodemon 可以监听项目文件的变动，当代码修改后，nodemon 会自动重启项目，方便调试

使用：

```
node app.js  -> nodemon app.js
```



### 路由

express 中，**路由指的是客户端请求与服务器处理函数之间的映射关系**，路由分为三部分：

```
app.METHOD(PATH, HANDLER)

// app.get('/', function(req, res){res.send('xxx')})
```

当请求到达服务器后，需要先经过路由的匹配，只有匹配成功之后，才会调用对应的处理函数（请求类型和URL）

客户端请求会按定义的先后顺序进行匹配，当请求类型和请求的 URL 同事匹配成功时，才会调用对应的处理函数

### 模块化路由

拆分代码，方便对路由进行模块化管理，将路由抽离为单独的模块，整个过程为：

1. 创建路由模块对应文件
2. 调用 express.Router() 创建路由对象
3. 向路由对象挂载具体路由
4. 使用 module.exports() 向外共享路由对象
5. 使用 app.use() 函数注册路由模块

### 按需导入模块化路由

例如，创建模块化路由文件 `express-router.js`:

```js
const express = require('express')

const router = express.Router()

router.get('/user/list', (req, res)=>{
    res.send('get.')
})

router.post('/user/list', (req, res)=>{
    res.send('post.')
})

module.exports = router
```

在 `express-router-index.js`中导入路由模块：

```js
const express = require('express')

const app = express()

const router = require('./express-router')
app.use(router);

app.listen(80, ()=>{
    console.log('http://127.0.01')
})
```

### 通配符处理

使用`*`可以处理多个请求，例如：

```
app.get('/m*', func)
```

此时所有以 m 开头的请求都会被 func 处理

### 错误请求处理

express 会根据定义的顺序逐一匹配所有的路由，如果所有的路由都无法匹配请求，就会显示 Cannot GET ... ，为了避免这种情况可以在最后添加处理错误处理的路由：

```js
app.get('*', (req, res)=>{
  res.send("404 not found")
})
```

**该路由一定要放在最后**，否则它可以匹配任何一条路由

### 中间件

中间件 middleware 是业务流程的中间处理方法，中间件的作用：处理服务请求的时候，需要进行许多细节处理，例如解析请求体，报文报头，设置 Cookie 等，为了避免每次重复这些代码，所以将这些固定功能的代码封装起来，每次访问路由后，自动执行用到的中间件，并因此出现很多公开使用中间件，方便 node 开发

对请求进行预处理：客户端向服务器发送请求 -> 中间件1  -> 中间件2  -> 处理完毕，响应这次请求

中间件本质上就是一个 function，格式为：

```js
app.get('/', function(req, res, next){
	...
	next()  // 必须包含 next()方法
})
```

next()方法将处理转到下一个中间件

### 全局生效的中间件

请求到达服务器之后，总能触发的中间件为全局生效的中间件，使用 `app.use()`进行注册

```js
const mv = function(req, res, next){
    console.log('test');
    next()
}

app.use(mv)
```

多个中间件之间共享同一份 req 和 res， 因此可以在上游的中间件中，统一为 req 和 res 添加自定义属性或方法，供下游的中间件使用，例如，要获得每次 get 或 post 请求到达的时间，可以在全局中间件中获得当前时间，然后将时间赋值添加为 req 的属性

上述定义可以简写为：

```js
app.use((req, res, next) => {
  console.log('测试简化全局中间件')
  next()
})
```

如果定义多个全局中间件，请求到达服务器后，会按照中间件定义的顺序依次进行调用

Tip：注意中间件与路由的定义顺序，如果中间件注册在路由之后，那么该中间件并不会执行，因此，**中间件注册应在路由注册之前！！！！**

**请求到达服务器后会根据全局中间件的顺序进行执行**

### 局部生效的中间件

局部生效的中间件：只在特定请求中执行，可以在路由中定义：

```js
// 在特定get / post 中传入 中间件名称表示只在这个过程中执行
app.get('/a', mv, (req, res) => {
    res.send(req.method + 'a page')
})
```

顺序使用多个局部中间件：

```js
// []内表示多个中间件 也可以不使用 []
app.get('/', [mv, mv2], (req, res) => {
    res.send(req.method + ' main page')
})
```

### 路由注意事件

- **一定要在注册路由之前注册中间件**

- 可以连续调用多个中间件处理

- 一定要在**最后调用 next（）函数**

- **连续调用多个中间件的时候，中间件之间共享 req 和 res 对象**

  - 因此可以将后续中间件中要使用的数据挂在 req 对象上：

    ```js
    // 在第一个中间件函数中挂 startTime 属性
    const local1 = (req, res, next) => {
      req.startTime = +new Date()
      console.log('local middleware function 1')
      next()
    }
    
    const local2 = (req, res, next) => {
      console.log('local middleware function 2')
      next()
    }
    
    // 路由
    // 在路由中使用 startTime 属性
    app.post('/test', [local1, local2], (req, res) => {
      console.log(req.startTime)
      res.send('post request received.')
    })
    ```

    



### 中间件分类

- 应用级别的中间件：通过 app.use() app.get() 等方式绑定到app实例上的中间件

- 路由级别的中间件：绑定在 router 实例：

  ```js
  const router = Router()
  
  router.use(function(req, res, next){
      console.log('router midware')
      next()
  })
  ```

- 错误级别的中间件，必须要有四个参数（err，req，res，next）

  ```js
  app.get('/', function(req, res){
      throw new Error('ERROR!!!')
      res.send('main page')
  })
  
  app.use(function(err, req, res, next){
      console.log(err.message)
      res.send(err.message)
  })
  ```

  **注意错误级别中间件必须定义在所有路由之后**

- express 内置中间件

  - express.static 托管静态资源 HTML文件 图片 CSS样式等

  - express.json  解析JSON格式的请求数据 (req.body 可以获得传入的 json 或 url-encoded数据)

    ```js
    // 如果发送的数据为 json 没有定义json解析的话 直接打印 req.body 显示的结果为 undefined
    app.use(express.json())
    
    app.get('/', (req, res) => {
        console.log(req.body)
        res.send('msg')
    })
    ```

    

  - express.urlencoded 解析 URL-encoded 格式的请求数据

    ```
    app.use(express.urlencoded({extended: false}))
    ```

- 第三方中间件 ： 由第三方开发，使用需要安装配置（安装 -> reqiure -> app.use()）

### 自定义中间件

案例： 定义中间件，完成 post 提交的数据

1. 定义中间件
2. 监听 req 的 data 事件
   1. 如果数据量较大 无法一次性发送，客户端会将数据集逆行切割，分批发送到服务器，因此 data 事件可能会触发多次，每次获得一部分数据，需要最后手动对数据进行拼接
3. 监听 req 的 end 事件：标识数据已经全部发送完毕
4. 使用 querystring 模块解析请求数据
5. 将解析的数据对象挂在为 req.body
6. 将自定义中间件封装



### CORS跨域资源分享

接口跨域问题的解决方法：

- CORS： 推荐使用方法
- JSONP：有缺陷，只支持 GET 请求

使用 cors 中间件解决跨域问题： 

1. `npm install cors` 安装中间件
2. `const cors = require('cors')`导入中间件
3. 在路由前调用 `app.use(cors())` 配置中间件

CORS（Cross-Origin Resource Sharing）由一系列 HTTP 响应头组成，这些响应头决定了浏览器是否阻止前端JS代码跨域获取资源 --》 **浏览器的同源安全策略默认情况下会阻止网页跨域获取资源**，但是如果接口服务器配置了CORS相关的HTTP响应头，就可以解除浏览器端的跨域访问限制

CORS的主要特点：

- CORS在服务器端进行配置，客户端浏览器无需处理
- 存在兼容性问题（Chrome4+, FireFox3.5+)

### CORS响应头部设置

#### Access-Control-Allow-Origin

用于设置允许访问该资源的外域URL， 例如：

```
res.setHeader('Access-Control-Allow-Origin', 'http://xxxx')
// 表示只允许访问 http://xxxx开头的请求

res.setHeader('Access-Control-Allow-Origin', '*')
// *为通配符 表示允许来自任何域的请求
```



#### Access-Control-Allow-Headers

请求头设置，默认设置下CORS支持客户端向服务器发送9个请求头：

Accept, Accept-Language, Content-Language, DPR, Downlink, Save-Data, Viewport-Width, Width, Content-Type

如果想要从客户端向服务器发送额外的请求头信息，需要在服务器中对额外的请求头进行声明：

```
res.setHeader('Access-Control-Allow-Headers', 'Content-Type, X-Custom-Header')
```



#### Access-Controls-Allow-Methods

设置允许的请求类型

默认情况下，CORS仅支持 GET、POST和HEAD请求

如果希望通过 PUT，DELETE 等方式请求资源，需要设置实际其ing求允许的HTTP方法：

```
res.setHeader('Access-Controls-Allow-Methods', 'GET, POST, PUT, HEAD, DELETE')

// 同样使用 * 表示允许全部的方式
```

### CORS请求的分类

- 简单请求：
  - 请求方式为 GET POST HEAD 之一
  - http头部为默认的9个请求头，无自定义头部
- 预检请求
  - 请求方式为 GET， POST， HEAD之外的类型
  - 请求头中包含自定义的头部
  - 向服务器发送了 application/json格式的数据
  - 其特点为：浏览器与服务器及逆行正式通信之前，浏览器会先发送 OPTION 请求进行预检，服务器响应预检请求之后，才会发送真正的请求，并且携带真实数据



## 数据库与身份认证

### 关系型数据库和非关系型数据库

关系型数据库，例如 MySql、Sql Server以及Oracle等

非关系型数据库， NoSQL（Not Only SQL），表示对传统关系型数据库的扩充，主要是针对当前网页数据量增大的情况做出的改进，特点为：

- 对于数据量非常大的时候，使用关系型数据库效率较低，使用NoSql可以提高访问效率
- 易于拓展
- 数据结构灵活

非关系型数据库的分类有键值存储数据库（Redis），列存储数据库（HBase），文档存储数据库（MongoDB）以及图数据库等

### 创建数据库

使用 MySql WorkBench 操作数据库，注意其数据类型以及一些字段的特殊标识：

- TINYINT(1) 布尔值
- PK（Primary Key）： 主键
- NN（Not Null）：值非空
- UQ（Unique）：值唯一
- AI（Auto Increment）：自动增长



### 数据库基本操作

查询：

```sql
-- select * from table
select username, password from users
select * from users where id=3
```

插入数据：

```sql
insert into users (username, password, status) values ('ZhaoLiu', 'zl', 0)
```

更改数据：

```sql
update users set password='11111' where id = 3
-- 更改多列数据
update users set password='33333', status=1 where id=4
```

删除数据：

```sql
-- 注意一定要加删除的条件 否则容易删除整张表
delete from users where id = 4
```

### where语句

限定查询、修改等操作的条件，其运算符包括等于、不等于、大于小于等等，注意几个较为特殊的运算符：

- `<>`或者`!=`都可以用于表示不等于
- 注意SQL中等于的比较是`=`
- `between`用于限定某个范围
- `like`搜索某种模式，用于模糊匹配

### and 和 or

分别表示条件同时满足和只满足任意一个条件即可

### 排序

使用 order by 可以对查询结果进行**升序排序**

```sql
select * from users order by id asc
```

其中 `asc` 表示升序，可以省略不写，如需要降序排序，可以使用 `desc`

**多重排序**：

```sql
select * from users order by status desc, username asc
```

使用`,`分隔多个排序关键字

### count  和 as

使用 count 关键字统计数据条数

```sql
select count(*) from users where status=0
```

使用 as 设置别名，例如为count的结果设置别名：

```sql
select count(*) as total from users where status=0
```

也可以使用 as 为普通查询结果设置别名：

```sql
select username as uanme from users
```



## Node.js 操作 MySql

### 基本步骤

1. 安装操作 MySql 数据库的第三方模块 （MySql）
2. 通过 MySql 模块连接到数据库
3. 通过 MySql 模块执行 Sql 语句

安装 MySql 模块：

```
npm i mysql
```

### 连接数据库与配置

```sql
// 测试 MySql 的连接
const e = require('cors');
const mysql = require('mysql');

// 建立 MySql 数据库连接并配置
const db = mysql.createPool({
    host: '127.0.0.1',
    user: 'root',
    password: 'admin123',
    database: 'my_dv_01',
});

// 检测数据库是否正常工作
db.query('SELECT 1', (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    console.log(results);
})
```



### sql语句执行

#### 查询语句

select 查询语句返回的是一个数组

```js
const sqlStr = 'select * from users';
db.query(sqlStr, (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    console.log(results);
});
```

#### 插入语句

插入语句：

```js
const user = {username: 'Qian', password: 'qian', status: 0};
// 通过占位符方式将实际数据传入
const sqlStr = 'insert into users (username, password, status) values (?, ?, ?)';

db.query(sqlStr, [user.username, user.password, user.status], (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    // 判断语句执行结果是否正确 如果实际影响到的行数为1则执行成功
    if(results.affectedRows === 1){
        console.log('Insert Done.');
    }
});
```

如果待插入的数据对象的每个属性与数据表的字段一一对应，则可以使用简化的插入语句：

```js
const user = {username: 'Sun', password: 'sun', status: 0};
// ? 表示占位符
const sqlStr = 'insert into users set ?';

// 直接将数据对象当作占位符的值
db.query(sqlStr, user, (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    if(results.affectedRows === 1){
        console.log('Insert Done.');
    }
})
```



#### 更新语句

使用普通方法逐属性更新：

```js
const user = {username: 'Qi', password: 'iiii', id: 14};
// 使用占位符代替数据
const sqlStr = 'update users set password=?, username=? where id=?';

db.query(sqlStr, [user.password, user.username, user.id], (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    if(results.affectedRows === 1){
        console.log('Update Done.');
    }
});
```



简化写法：

```js
const user = {id: 14, username:'QIAN', password: 'qqqq'};

const sqlStr = 'update users set ? where id=?';

db.query(sqlStr, [user, user.id], (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    if(results.affectedRows === 1){
        console.log('update done.');
    }
});
```



#### 删除语句

删除的时候一般都会根据 `id` 进行删除（`id`都具有唯一性）：

```js
const sqlStr = 'delete from users where id=?';

db.query(sqlStr, 15, (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    if(results.affectedRows === 1){
        console.log('Delete done');
    }
});
```

**实际场景**： 实际情况下，由于用户存在误删等操作，因此为了保险起见，**更推荐使用标记删除的方式**，即在数据表中设置 status 这样的状态字段，用于标记当前数据是否被删除，当用户执行了删除操作时，这样用户执行删除的动作时，实际上执行的是 update 语句

```js
const sqlStr = 'update users set status=1 where id=?';

db.query(sqlStr, 12, (err, results)=>{
    if(err){
        return console.log(err.message);
    }
    if(results.affectedRows === 1){
        console.log('Delete done.');
    }
});
```

### 数据库操作

#### 关于返回数据格式

使用 select 返回的数据格式为：

```
res = [
  RowDataPacket { month: 'Apr', grade: 45 },
  RowDataPacket { month: 'Aug', grade: 68 },
  RowDataPacket { month: 'Dec', grade: 88 },
 ]
```

但是实际使用的时候，可以忽略中间的 RowDataPacket这一层，直接使用即可：

```
res[i].month   /   res[i].grade
```

如果想要对象数组的格式，可以使用JSON处理：

```
let tmpRes = JSON.parse(JSON.stringify(results));
```

或者：

```
let obj = {};
obj[results[i].month] = results[i].grade;
res.push(obj);
```

对于转化为 JSON对象数组形式的数据，现有需求：将每个JSON对象第一个值抽出为一个数组，将第二个值也抽出为一个单独数组，例如：`{'month': '2011-01', num: 12}` 得到 `[2011-01], [12]`两个数组

做法如下：

```js
const jsonStr = '[{"name": "zs", "age": 12}, {"name": "lis", "age": 15}]'
const objArr = JSON.parse(jsonStr).map((item)=>{
    return Object.values(item)
})
console.log(objArr)  // [['zs', 12], ['lis', 15]]
const valList1 = objArr.map((item)=>{
    return item[0]
})  // ['zs', 'lis']
const valList2 = objArr.map((item)=>{
    return item[1]
})
```

**思路：**  首先得到 JSON 对象数组，利用 map 遍历所有的对象，并获得所有的值，此时获得的结果是一个二维数组，随后利用 map 对每个数组进行遍历取值，分别存入两个数组中即可

## 异步

### 回调函数

Node.js 异步编程的最直接体现就是回调函数，函数完成任务后会调用回调函数，例如：

```js
let a = 0
function funcPrint(x){
    console.log(x)
}

function timer(time, callback){
    setTimeout(() => {
        a = 6
        callback(a)        
    }, time)
}

console.log(a)
timer(2000, funcPrint)
```

几个概念：

- 同步：程序自上而下依次执行   ----   阻塞，只能顺序执行，如果中间某个地方耗时长，后续代码也无法执行
- 异步：可以同时执行多个操作  ----- 非阻塞，如果中间有操作耗时较长，后续代码会继续执行

**回调函数**：回调函数作为参数传递给另一个函数，并且会在另一个函数完成后立即执行，这个回调函数会在传给的函数内部执行

### 事件循环

Node.js 是单进程单线程应用程序，其通过事件和回调支持并发，Node.js 单线程类似进入一个 `while(true)`的事件循环，直到没有事件观察者，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数

回调地狱：多次回调函数嵌套

解决回调地狱的方案：

- 使用 Promise
- 使用 async-await
- 使用 async.js 库

### Promise 对象

Promise构造函数示例：

```js
const pomis = new Promise(function(resolve, reject){
	if(操作成功){
        resolve(value)
    }else{
        rejct(error)
    }
})
```

Promise 构造参数接收一个函数作为参数，该函数有两个参数，分别为 resolve 和 reject，其作用为：

- resolve： 将 promise 对象由  pending 状态转变为 fufilled 状态 （成功），**其作用为异步操作成功时调用，并将结果传递出去**（可以想象为改函数替你保存异步操作的结果，因此最后要 resolve(res)）
- reject：将 promise 对象由 pending 状态转变为 rejected 状态（失败），**其作用为将异步操作失败所报的错误作为参数传递出去**

Promise 实例生成以后可以使用 then 方法指定 resolve 和 rejected 状态的回调函数（可选），并且他们**都接收promise对象传出的值作为参数**：

```js
promise.then(function(value){
    // 状态转变为 resolve 时调用
}, function(err){
    // 装填转变为 rejected 时调用
})
```

因此，对于promise构造函数中的 resolve 和 reject 函数，分别用于传递实际操作结果和错误，并且结果作为参数传递到 then 函数中，例如数据库的操作：

```js
const promise = new Promise((resolve, reject)=>{
    db.query(sqlStr, (err, results) =>{
        if(err){
            // 如果数据库操作出现错误 将错误通过 reject 函数传递出去
            // 相当于什么也不做 只是做了个转发 resolve(err){return err}
            return reject(err)
        }
        // 成功执行的结果通过 resolve 函数传递出去
        resolve(results)
    })
})

promise.then((res)=>{
    // 接收 resolve 函数的返回结果 res = resolve()
    console.log(res)
}, (err)=>{
    // 接收 reject 的返回的结果
    console.log(err.message)
})
```

一般来说，调用 resolve 或者 reject 函数之后， promise 对象的操作就结束了，之后的处理应该在 then 中进行，所以 resolve 和 reject 函数后面不应该有代码，因此最好在两个函数前面加上 return 语句，这样就不会有意外：

```js
new Promise((resolve, reject)=>{
	success{
        return resolve(res)
    }
    fail{
        return reject(err)
    }
})
```

#### then 方法 (Promise.prototype.then())

Promise 实例具有 then 方法，其作用是为 promise 实例添加状态改变时候的回调函数，可以传入两个函数作为 resolved 和 rejected 状态的回调函数，then 函数也可以写为链式形式，指定一组按次序调用的回调函数，即：

```
function().then(){
	// ...
}.then(){
	// ..
}
```

其中前一个回调函数也可以是一个 promise 对象，此时后一个 then 方法会等待前一个 promise 对象变为 resolved 或者 rejected 状态才会调用

#### catch 方法 （Promise.prototype.catch()）

用于指定错误的回调函数，`then` 方法参数中的第二个函数表示 rejected 状态时的操作，这一函数也可以单独写出成为 `catch` 方法，即用来捕获异步操作过程中的错误，此外如果 `then` 方法指定的回调函数如果出现错误，也会被 `catch` 方法捕获。此外，**更推荐使用 `catch` 方法捕获错误，而不是再 `then` 方法中定义 rejected 状态的回调函数**，例如：

```js
function getBooksAjax(url){
    const promise = new Promise((resolve, reject)=>{
        const xhr = new XMLHttpRequest()
        let ind = 1
        xhr.open('GET', url+'?id='+ind)
        xhr.send()
        xhr.onreadystatechange = ()=>{
            if(xhr.readyState !== 4){
                return;
            }
            if(xhr.status === 200){
                // 推荐写成 return 的方式
                return resolve(xhr.responseText)
            }else{
                return reject(new Error(xhr.statusText))
            }
        }
    })
    return promise
}

getBooksAjax(url).then((value)=>{
    console.log(value)
}).catch((err)=>{
    // 推荐使用 catch 捕获rejected的返回结果
    console.log(err)
})
```

#### finally方法（Promise.prototype.finally()）

finally 方法不管 promise 最终是哪种状态都会执行，finally 中的操作与 promise 的状态并无关系，并且该方法的回调函数不接受参数，例如：

```js
getBooksAjax(url).then((value)=>{
    console.log(value)
}).catch((err)=>{
    console.log(err)
}).finally(()=>{
    console.log('done.')
})
```

#### Promise.all()方法

Promise.all()方法用于将多个 promise 实例包装成一个新的 promise 实例，最终新的 promise 对象的状态由所有的 promise 实例决定：

- p1,p2,...pn 所有的状态全部为 fulfilled ，最终状态才变为 fulfilled
- p1,p2,...pn 有一个状态变为 rejected，最终状态就i会变为 rejected

例如：

```js
const promises = [1, 2].map(function(id){
    return getBooks2(url, id)
})
Promise.all(promises).then((value)=>{
    console.log(value)
}).catch((err)=>{
    console.log(err)
}).finally(()=>{
    console.log('done')
})
```

### async 和 await

`async` 声明 function 是一个异步函数，返回一个 promise 对象，使用与 promise 语法类似，可以使用 then 方法添加回调函数，其中 `async` 函数内部返回的值，会成为 then 方法回调函数的参数

`await` 操作符只能在异步函数 async function 内部使用，如果一个 Promise 被传递给一个 await 操作符，await 将等待 promise 正常处理完成并返回其处理结果，也就是会阻塞后面的代码，如果等待的不是 promise 对象，则会返回该值本身



方法：

```js
function printBooks(){
    function getBooks(){
        const xhr = new XMLHttpRequest()
        xhr.open('GET', url+'?id=1')
        xhr.send()
        xhr.onreadystatechange = function(){
            if(this.readyState !== 4){
                return;
            }
            if(this.status === 200){
                console.log(this.responseText)
            }else{
                console.log(this.statusText)
            }
        }
    }
    getBooks()
    console.log('done')
}

printBooks()
```

此时，由于请求信息的函数进入 event table， `console.log('done')` 会先执行，所以输出的结果是：

```
done
{book info}
```

使用 async - await 后：

```

```



##  前后端身份认证

### Web 开发模式

web开发模式的分类：

- 基于服务器渲染的传统 web 开发模式
- 基于前后端分离的新型 web 开发模式

#### 服务器端渲染的 web 开发模式

服务器端渲染的概念：服务器发送给客户端的 HTML 页面，是服务器通过字符串拼接方式动态生成的，客户端不需要使用   Ajax 这样的技术额外请求页面的数据

优点：

- 前端耗时少
- 有利于 SEO， 服务器返回的是完整的页面，所以爬虫更容易获得信息，更易被搜索发现

缺点：

- 占用服务器资源：服务器需要控制HTML页面的生成
- 不利于前后端分离，开发效率低



#### 前后端分离的 web 开发模式

后端只负责提供 API 接口，前端使用 Ajax 调用接口

优点：

- 开发体验好：前端只负责页面开发，后端专注于 api 开发
- Ajax 可以实现局部页面的刷新，用户体验好
- 减轻了服务器端的渲染压力

缺点：

- 不利于 SEO 完整页面是在客户端动态生成的，所以爬虫无法获取有效信息（使用VUE，React等框架的 SSR（server side render）可以解决这一问题）

#### 应用场景

- 主要功能是展示，没有复杂交互的项目（例如企业级网站），适合使用服务器渲染方式
- 交互性比较强，不需要考虑SEO的项目（各种后台管理项目），适合前后端分离的开发模式

某些项目也会同时兼顾首页渲染速度和前后端分离开发的效率，采用首屏服务器渲染 + 其他页面前后端分离的开发模式

#### 身份认证

通过一定的手段，对用户身份验证，例如密码登录，邮箱登录或者二维码

- 对于服务器端渲染的方式推荐使用 Session 认证机制
- 前后端分离推荐使用 JWT 认证机制

#### Session 认证机制

HTTP协议的无状态性：客户端的每次HTTP请求是独立的，连续多个请求之间没有直接的关系，服务器不会主动保留请求的状态
Cookie ： 存储在浏览器中一段不超过 4KB 的字符串，由一个 名称-name，一个值-value和用于控制Cookie有效期、安全性、适用范围的可选属性组成 

不同域名下的 Cookie 各自独立，每当客户端发起请求的时候，会自动把当前域名下的所有未过期的 Cookie 一同发送到服务器，Cookie特点为：

- 自动发送
- 域名独立
- 过期时限
- 4KB限制

客户端第一次请求服务器的时候，服务器会通过响应头的形式将一个身份认证的 Cookie 发送到客户端，客户端会自动将Cookie保存在浏览器中，随后每次请求服务器的时候，浏览器会自动将身份认证相关的 Cookie 通过请求头的形式发送给服务器吗，服务器即可验明客户端身份

Cookie不具有安全性： Cookie保存在浏览器中，而且浏览器也提供了读写 Cookie 的 API，因此 Cookie 容易被伪造，因此重要且隐私的数据不要使用 Cookie 存储

提高身份认证的安全性：Session认证机制 -- 会员卡 + 刷卡认证

Session 的工作原理：

1. 浏览器登录，提交账号密码
2. 服务器验证账号密码，并保存用户信息，同时生成对应的 Cookie 字符串发送给客户端
3. 浏览器保存Cookie
4. 浏览器再次请求，发送同域名下的所有 Cookie
5. 服务器根据 Cookie 查找对应的用户信息，身份认证成功后发送响应内容

#### express中使用session认证

安装：

```
npm i express-session
```

配置：

```js
app.use(ssession({
    // secret属性值可以是任意字符串
    secret: 'anything',
    // 两个属性为固定写法
    resave: false,
    saveUninitializaed: true
}));
```

使用：

```js
// 登录接口
app.post('/api/post', (req, res)=>{
    if(req.body.username !== 'admin' || req.body.password !== '0000'){
        return res.send({status:1, meg:'default'});
    }
});

// 将用户信息 存储到 session 中
req.session.user = req.body;
// 将用户的登陆状态 存储到session中
req.session.islogin = true;

res.send({status: 0, msg: 'success'});
```

其中 `req.session`属性只有在配置session后才会有，可以通过它访问session对象，并向其存储数据，可以通过session对象获取的数据来对登录用户进行判断：

```js
app.get('/api/username', (req, res)=>{
    if(!req.session.islogin){
        res.send({status: 1, msg: 'fail'});
    }
    res.send({status: 0, msg: 'success', username: req.session.user.username});
});
```

可以使用 `req.session.destroy()`  函数清空服务器保存的 session 信息：

```js
// 退出登录的接口
app.post('/api/logout', (req, res)=>{
    // 清空当前客户端对应的 session 信息
    req.session.destroy();
    res.send({
        status: 0,
        msg: 'login out',
    });
});
```

#### JWT 认证机制

Session 认证的局限性： session 认证需要配合 Cookie 才能实现，由于 Cookie 默认不支持跨域访问，所以涉及到前端跨域请求后端接口的时候需要一些额外的配置，这样才能实现跨域的 Session 认证。

因此：

- 不存在跨域请求的时候，推荐使用 session 机制
- 出现跨域请求接口的时候，推荐使用 JWT 认证机制

JWT：JSON Web Token 是目前最流行的跨域认证方案

#### JWT 工作流程

1. 客户端登录，提交账号和密码
2. 服务器验证账号和密码，验证通过之后，服务器会将用户的信息对象，经过加密之后生成 token 字符串并发送给客户端
3. 客户端将 token 保存在 localStorage 或 SessionStorage 中
4. 客户端再次发起请求的时候，通过请求头的 authorization 字段，将 token 发给服务器
5. 服务器还原token字符串并检查用户身份，随后根据当前用户生成特定的响应内容并返回响应内容

JWT的组成部分：

- Header
- Payload： 真正的用户信息，这是用户经过加密后的字符串
- Signature：Header 和 Signature 是安全性相关额部分， 用于保证 Token 的安全性

#### JWT 使用方式

在第一次完成客户端和服务器的通信之后，每次客户端与服务器进行交互，都要带上 token 字符串从而进行身份认证，推荐的做法是把 JWT 字符串放在 HTTP请求头的 Authorization 字符按中：

```
Authorization: Bearer <token>
```

#### 在 express 中使用 JWT

##### 安装依赖包

- jsonwebtoken：用于生成 JWT 字符串
- express-jwt：用于将 JWT 字符串解析还原成 JSON 对象

```
npm i jsonwebtoken express-jwt

const jwt = require('jsonwebtoken');
const expressJWT = require('express-jwt')
```

##### 定义 secret 密钥

为了保证 JWT 字符串的安全性，防止 JWT 字符串在网络传输过程中被别人破解，需要专门定义一个用于加密和解密的 secret 密钥，该密钥为字符串，例如：

```
const secretKey = 'abcd NN +_+'
```

##### 登录成功后生成 JWT 字符串

使用 `jsonwebtoken.sign()`方法可以将用户信息加密成 JWT 字符串

```js
const tokenStr = jwt.sign({username: userInfo.username}, secretKey, {expiresIn: '30s'});
app.post('/api/login', (req, res)=>{
    res.send({
        status: 200,
        message: 'login success',
        // 调用 sign() 方法生成 JWT 字符串 参数分别为： 用户信息、加密密钥、配置对象有效期30s, 30h
        token: tokenStr,
    });
});
```

##### JWT 字符串还原为 JSON 对象

通过 `jwt({secret: secretKey})` 传入密钥，并可以使用 `unless()` 函数来指定哪些接口不需要访问权限，例如：

```js
app.use(expressJWT({secret: secretKey})).unless({path: [/^\/api\\/]})
```

这里 `unless()` 函数通过正则表达式指定 `/api` 下的接口都不需要访问权限

##### 使用 `req.user` 查看用户信息

配置成功 expressJWT 后，会自动向 `req` 对象中挂载一个 `.user` ，其中包含了对象的信息（注意这里的对象信息是之前通过 `sign()`方法添加的）

##### 捕获解析 JWT 失败之后产生的错误

使用 express-jwt 解析 token 字符串的时候，如果字符串发送的 token 字符串过期或者不合法，会产生一个解析失败的错误，影响项目的正常运行，可以通过 express 的错误中间价捕获这个错误并进行处理：

```js
app.use((err, req, res, next)=>){
    // 解析失败导致的错误
	if(err.name === 'UnauthorizedError'){
    return res.send({status: 401, message: '无效的token'}),
	}
    // 其他原因导致的错误
    res.send({status: 500, messageL '未知错误'})
}
```



## 其他

### package.json 和 package-lock.json

`npm init -y`初始化项目后会生成 `package.json` 文件用于保存安装模块的信息。这样在拉取项目的时候可以根据保存的信息安装项目的相关依赖，但问题在于 **`package.json`只能锁定大版本，即只负责大版本一致的情况下，安装最新的小版本**，这样并不利于项目的稳定性，因此出现了 `package-lock.json` 文件用于保存依赖包的版本号、下载地址以及子依赖包等信息，并且**可以锁定小版本号**，从而保证在新拉取项目的时候也可以与之前保持一致。

这样，当执行 npm install 命令的时候，首先从 `package.json`中获取模块名称，随后在 `package-lock.json` 中获取版本号，随后进行下载更新。

### Linux 安装 mysql注意点

1.  注意版本一致，使用命令 `cat /etc/redhat-release`查看版本

2. 安装mysql依赖包的时候出现依赖错误：

   ```linux
   error: Failed dependencies: mariadb-libs is obsoleted by mysql-community-libs-8.0.28-1.el7.x86_64
   ```

   解决方法为：先卸载 mysql-libs ，再执行安装：

   ```
   yum remove mysql-libs
   rpm .....
   ```

3. 安装 mysql 服务器时失败：

   ```
   error: Failed dependencies:mysql-community-icu-data-files = 8.0.28-1.el7 is needed by mysql community-server-8.0.28-1.el7.x86_64
   ```

   解决方法为:

   ```
   rpm -ivh mysql-community-server-8.0.28-1.el7.x86_64.rpm --force --nodeps
   ```

   安装的时候忽略依赖关系

4. 启动服务器之后，查看安装  mysql 生成的临时密码：

   ```
   cat /var/log/mysqld.log
   ```

参考：

```
https://blog.csdn.net/xhmico/article/details/125197747
```

