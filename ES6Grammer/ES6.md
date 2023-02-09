# ES6 模块化与异步

## ES6 模块化

Node.js 模块化实现方案：遵循 CommonJS 的模块化规范：

- 导入其他模块使用 require 方法
- 模块对外共享成员使用 module.exports 对象

除了 CommonJS 规范，还有适用于不同场景的模块化规范，ES6是官方在这些社区规范的基础上整合的结果，用于降低开发者学习的成本

ES6 模块化规范中定义：

- 每个 js 文件都是一个独立的模块
- 导入其他模块的成员使用 import 关键字
- 向外共享模块成员使用 export 关键字

## Node.js 中使用 ES6 模块化语法

### 配置

Node.js 中默认支持的是 CommonJS 模块化语法规范，如使用 ES6 模块化语法，需要进行配置：

- 确保 Node.js 的版本大于等于 v14.15.1
- 在 package.json 的根节点中添加 `"type": "module"`节点

### 基本语法

ES6 模块化主要包含三种用法：

- 默认导出与默认导入
- 按需导出与按需导入
- 直接导入并执行模块中的代码

#### 默认导出与默认导入

默认导出的语法格式：

```
export default {变量， 方法}
```

默认导入的语法格式：

```
import 接受名称 from 模块标识符
```

例如，导出变量与方法：

```js
// 定义私有变量
let n1 = 10
let n2 = 20

// 定义模块私有方法
function show() {
  console.log('modularization')
}

// 注意 export default 只允许使用一次
export default {
  n1,
  show,
}
```

默认导入

```js
import m1 from './01-default-export.js'

console.log(m1)
// 打印结果为：
// { n1: 10, show: [Function: show] }
```

**这里注意一个细节：**，如果默认导出方式只有一个内容需要导出，最好不要使用`{}`包裹起来，例如：

```
let a
export default {a}
```

此时导入该文件：

```
import a from 'test.js'
```

如想使用变量 a， 则必须使用对象的方式：

```
a['a']
a.a
```

原因在于使用 default {a} 导出的是一个对象，该对象中有一个成员为 a，如果不添加大括号，则可以直接使用 a 

#### 按需导出与按需导入

按需导出的语法：

```
export 成员
```

按需导入的语法：

```
import 成员名 from 模块名
```

例如：

```js
// 按需导出
export let s1 = 'a'
export let s2 = 12
export function print() {
  console.log('some texts')
}
```

导入：

```js
// 按需导入
import { s1, s2, print } from './01-default-export.js'

console.log(s1)
console.log(s2)
print()
```

按需导出与按需导入的注意事项：

- 可以使用多次按需导出

- 按需导入的成员必须和按需导出的名称保持一致

- 按需导入时，可以使用 `as`关键字重命名

  ```js
  import { s1, s2 as ss2, print } from './01-default-export.js'
  ```

- 按需导入和默认导入可以同时使用

  ```js
  import m1, { s1, s2 as ss2, print } from './01-default-export.js'
  ```

  这里 m1 所对应的就是 01-default-export 模块中默认导出的成员（花括号内为按需导入的成员，默认导入在花括号外边）

Tips：使用按需导出的方式导入路由文件

路由文件：

```js
import express from 'express'

// 导出 router 要写在定义处
// 在文件末尾 使用 export router 是错误的
export const router = express.Router()

router.get('/gettest', (req, res) => {
  res.send('get request.')
})

router.post('/posttest', (req, res) => {
  console.log(req.body)
  res.send('post request.')
})
```



#### 直接导入

如果想要直接使用模块的代码而无需使用成员，可以使用直接导入的方法，直接使用 `import 模块名`

## Promise

### 回调地狱

回调地狱指的是大量嵌套的回调函数，其代码耦合高，难以维护，为了解决这一问题，ES6 中新增了 Promise 的概念

### 基本概念

- Promise 是一个构造函数
  - 通过`const p = new Promise()`创建实例
  - Promise 实例代表**一个异步操作**
- Promise.prototype 上包含一个`.then()`方法
  - 创建的 Promise 实例对象通过 .then()`方法访问 then 方法
  - then 方法用于预先指定成功和失败的回调函数
  - p.then(result=>{}, error=>{})，其中 result 指定成功的回调函数， error 指定失败的回调
  - 其中，成功的回调函数是必须的，失败的回调函数可选

### 异步读取文件

读取`files`文件夹下的三个文件，使用回调函数方式：

```js
/* 使用回调函数读文件 */
import fs from 'fs'

fs.readFile('./files/file1.txt', 'utf8', (err, val) => {
  if (err) {
    return console.log(err.message)
  }
  console.log(val)
  fs.readFile('./files/file2.txt', 'utf8', (err, val) => {
    if (err) {
      return console.log(err.message)
    }
    console.log(val)
    fs.readFile('./files/file3.txt', 'utf-8', (err, val) => {
      if (err) {
        return console.log(err.message)
      }
      console.log(val)
    })
  })
})

```

由于 Node.js 提供 fs 模块只支持通过回调函数读取文件，因此为了使用基于 Promise 的方式读取文件，需要使用 `then-fs`（直接使用`npm i then-fs`读取即可），使用`then-fs`的实现方式为：

```js
/* 使用 then-fs 基于 Promise 读取文件 */
import thenFs from 'then-fs'

thenFs.readFile('./files/file1.txt', 'utf8').then(
  (val) => {
    console.log(val)
  },
  (err) => {
    console.log(err.message)
  }
)
thenFs.readFile('./files/file2.txt', 'utf8').then(
  (val) => {
    console.log(val)
  },
  (err) => {
    console.log(err.message)
  }
)
thenFs.readFile('./files/file3.txt', 'utf8').then(
  (val) => {
    console.log(val)
  },
  (err) => {
    console.log(err.message)
  }
)

```

这种方式结构更清晰，便于修改，但是上述方式只实现了异步方式读取文件，并不能保证顺序（按照代码中读取的顺序打印出文件内容）

### 改进方法

Promise.then() 方法中如果返回了一个新的 Promise 实例，那么可以通过下一个 then() 方法继续处理，因此可以通过链式调用的方法顺序读取不同文件内容：

```js
import thenFs from 'then-fs'

thenFs
  .readFile('./files/file1.txt', 'utf8')
  .then((val) => {
    console.log(val)
    // 返回一个 Promise 实例 在后面的 then 方法中调用
    return thenFs.readFile('./files/file2.txt', 'utf8')
  })
  .then((val) => {
    console.log(val)
    return thenFs.readFile('./files/file3.txt', 'utf8')
  })
  .then((val) => {
    console.log(val)
  })

```

上述方式保证了读取文件的顺序

### 使用 catch 捕获错误

使用 catch 子句可以捕获过程中的错误信息，例如将上述代码改为：

```js
thenFs
    .readFile()
    .then()
    .then()
    .then()
    .catch((err)=>{...})
```

如果在读取三个文件的过程中出现错误，会跳转到 catch 子句捕获错误并打印，注意**将 catch 子句放在最后时，中间流程出现错误会中断所有代码的执行**，如果想要处理某个步骤的错误后继续正常执行后续代码，可以将 catch 子句提前（这里以处理第一个文件的错误为例）：

```js
thenFs.readFile().catch((err)=>{...}).then().then().then()
```

此时如果第一个文件的读取出错， 会在打印错误信息后继续读取文件2，3

### Promise.all()

Promise.all() 方法会发起并行的 Promise 异步操作，并且全部的异步操作结束后才会执行下一步 then 操作（等待机制），例如：

```js
import thenFs from 'then-fs'

// 定义异步操作数组
const promiseArr = [
  thenFs.readFile('./files/file1.txt', 'utf8'),
  thenFs.readFile('./files/file2.txt', 'utf8'),
  thenFs.readFile('./files/file3.txt', 'utf8'),
]

// 将数组中所有的异步操作全部执行完才会进行下一步
Promise.all(promiseArr)
  .then((val) => {
    console.log(val)
  })
  .catch((err) => {
    console.log(err.message)
  })
```

注意该方法中，数组定义的 Promise 实力的顺序就是最终打印的顺序，也可以使用数组接收结果：

```js
// 也可以使用数组的方式进行接收
Promise.all(promiseArr)
  .then(([val1, val2, val3]) => {
    console.log(val1)
    console.log(val2)
    console.log(val3)
  })
  .catch((err) => {
    console.log(err.message)
  })
```

### Promise.race()

与 Promise.all() 方法对应的是 Promise.race() 方法，该方法为“竞争”方法，所有的异步操作只要有一个完毕，代码就会继续执行接下来的流程：

```js
Promise.race(promiseArr)
  // promiseArr 数组中任何一个异步操作完成就会立即执行 then 方法
  .then((res) => {
    console.log(res)
  })
  .catch((err) => {
    console.log(err.message)
  })
```

### 封装定制的读文件方法

使用 New Promise() 实现异步读取文件：

```js
import fs from 'fs'

function getFile(fpath) {
  // Promise 接收两个参数 resolve 代表成功时的操作  reject 代表失败时的操作
  return new Promise(function (resolve, reject) {
    fs.readFile(fpath, 'utf8', (err, dataStr) => {
      // 如果文件读取失败 将 err 传递给错误处理函数 并 return (结束执行)
      if (err) return reject(err)
      resolve(dataStr)
    })
  })
}

// 调用方法时候指定 resolve 和 reject 对应的处理函数
getFile('./files/file11.txt').then(
  (val) => {
    console.log(val)
  },
  (err) => {
    console.log(err.message)
  }
)
```

## async/await

### 语法

async/await 是 ES8 中引入的新语法，用于简化 Promise 操作（.then 方法依然存在代码冗余的问题），例如：

使用 .then() 方法链式调用的方式：

```js
function getFile(fpath) {
  thenFs
    .readFile(fpath, 'utf8')
    .then((val) => {
      console.log(val)
    })
    .catch((err) => {
      console.log(err.message)
    })
}
```

使用 async/await 简化方式：

```js
async function getFile2(fpath) {
  const res1 = await thenFs.readFile('./files/file1.txt', 'utf8')
  console.log(res1)
  const res2 = await thenFs.readFile('./files/file2.txt', 'utf-8')
  console.log(res2)
  const res3 = await thenFs.readFile('./files/file3.txt', 'utf-8')
  console.log(res3)
}
```

对于 Promise 对象实例，可以使用 await 关键字获得其返回结果

### 注意

await 关键字与 async 必须配套使用，否则语法报错

**对于 async 定义的函数，在 await 关键字出现之前，代码同步执行，await 关键字出现后代码异步执行**，例如：

```js
import thenFs from 'then-fs'

console.log('a')
async function readFile() {
  console.log('b')
  const res1 = await thenFs.readFile('./files/file1.txt', 'utf8')
  console.log(res1)
  const res2 = await thenFs.readFile('./files/file2.txt', 'utf8')
  console.log(res2)
  const res3 = await thenFs.readFile('./files/file3.txt', 'utf8')
  console.log(res3)
  console.log('c')
}

readFile()
console.log('d')
```

上述代码的结果为：

```
a    - 同步
b    - 同步 
     -> 出现 await 关键字 之后代码异步执行，所以主线程退出函数
d    - 同步
     -> 同步代码执行完毕，接下来异步执行
111
222
333
c
```

## EventLoop

### JS

JS 是一门单线程执行的编程语言，同一时间只能进行一项任务，这种单线程执行任务队列的方式存在程序假死的问题，即如果一个任务非常耗时，后续任务就不得不一直等待

### 同步任务和异步任务

同步任务（synschronous）：

- 也叫做非耗时任务，其执行速度非常快，指的是在主线程上排队执行的那些任务
- 只有前一个任务执行完毕，才能执行后一个任务

异步任务（asynchronous）：

- 耗时任务，异步任务由 JS 委托给宿主环境（浏览器或node.js的服务器）执行
- 异步任务结束后，会通知 JS 主线程执行异步任务的回调函数

同步任务和异步任务的执行过程：

<img src="ES6.assets/image-20221013165316355.png" alt="image-20221013165316355" style="zoom:67%;" />

1. 同步任务由 JS 主线程依次执行
2. 异步任务委托给宿主环境执行
3. 已完成的异步任务对应的回调函数会被加入到任务队列中等待执行
4. JS 主线程的执行栈被清空之后，会读取任务队列中的回调函数，依次执行
5. **JS 主线程会不断重复上面过程 （EventLoop）**

## 宏任务与微任务

宏任务与微任务是 JS 对于异步任务的进一步划分：

宏任务（macrotask）：

- 异步 Ajax 请求
- setTimeout、setInterval
- 文件操作
- 其他宏任务

微任务（microtask）：

- Promise.then .catch .finally
- process.nextTick
- 其他微任务

<img src="ES6.assets/image-20221013195759583.png" alt="image-20221013195759583" style="zoom:67%;" />

宏任务与微任务的执行顺序：

每一个宏任务执行完成后， **都会检查是否存在待执行的微任务**，如果有，则执行完所有的微任务后，再执行下一个宏任务，例如：

```js
setTimeout(function () {
  console.log('1')
})

new Promise(function (resolve) {
  console.log('2')
  resolve()
}).then(function () {
  console.log('3')
})

console.log('4')
```

上述代码的输出顺序为 2431

- 首先执行同步任务（new promise 和 console.log(4）)
- 执行微任务（.then()）
- 执行下一个宏任务