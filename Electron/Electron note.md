Electron

## 安装常见问题

安装命令（安装在开发环境下）：

```
npm i electron -D
```

注意 Electron 的版本依赖于 Node.js 的版本，要注意匹配，安装指定版本的 electron：

```
npm i electron@19.0.0 -D
```

另外，安装过程出现`RequestError: unable to verify the first certificate`错误，可以尝试切换镜像地址（该问题参考：[electron 安装](https://www.cnblogs.com/tadx2/p/12259235.html)），可以找到`npmrc`文件中添加：

```
ELECTRON_MIRROR="https://cdn.npm.taobao.org/dist/electron/"
```

或者在 cmd 中使用命令添加：

```
npm config set ELECTRON_MIRROR=https://cdn.npm.taobao.org/dist/electron/
```

清除 npm 缓存（安装较大的包可能会报错）

```
npm cache clean --force
```

## 构建第一个案例

定义入口文件`main.js`：

```js
const { app, BrowserWindow } = require('electron')

const createWindow = () => {
  const win = new BrowserWindow({
    width: 1000,
    height: 800
  })

  // win.loadURL('https://www.zhihu.com/hot')
  win.loadFile('./test.html')
}
app.whenReady().then(createWindow)
```

在`package.json`中可以看到，入口文件默认为`index.js`，可以修改`main`节点的值为`main.js`，构建完成后，可以使用命令：`node_modules\electron\dist\electron.exe ./main.js`运行，也可以使用 nodemon 命令改进写法，例如在 package 文件中修改 script 节点内容为：

```
"start": "nodemon --exec electron ."
```

然后使用命令 `npm run start` 即可运行 electron 项目

## Electron 主进程与渲染进程

主进程：启动项目时运行的 `main.js` 或者 `index.js`就是主进程，在主进程运行的脚本可以创建 web 页面的形式展示 GUI，主进程只有一个

渲染进程：每个 Electron 的页面都在运行自己的进程，这样的进程被称为渲染进程（基于 Chromium 的多进程结构）

主进程使用 BrowserWindow 创建实例，主进程销毁后，对应的渲染进程也被终止，主进程与渲染进程通过 IPC 方式进行通信

## 隐藏警告信息

在 createWindow 中添加：

```js
process.env['ELECTRON_DISABLE_SECURITY_WARNINGS'] ='true'
```

在 html 页面中使用（由浏览器决定）：

```html
<meta http-equiy="Content-Security-Policy" content="default-src 'self'; img-src 'self' data:;script-src 'self'; style-src 'self' 'unsafe-inline'">
```

## 打开开发者工具

在 createWindow 函数中添加代码：

```
win.webContents.openDevTools()
```

## 测试使用文件功能

创建脚本文件 `app.js`，用于创建文件：

```js
const fs = require('fs')

fs.writeFile("./test.txt", 'some content.', () => {
  console.log('done.')
})
```

在 html 文件中引入：

```html
<script type="module" src="./render/app.js"></script>
```

使用`type=module`启用 ES6 语法进行模块导入

在 createWindow 函数中启用 nodejs 语法：

```js
const createWindow = () => {
  const win = new BrowserWindow({
    width: 1000,
    height: 600,
    webPreferences: {
      // 开发中不要使用
      nodeIntegration: true,
      contextIsolation: false
    }
  })
```

此时运行项目，可以看到指定文件夹下有 test.txt 文件生成

注意上述设置项只是测试时使用，**在实际开发中不要使用**

## 主进程生命周期函数

主进程生命周期函数，例如：

```js
app.on('window-all-closed', () => {
  console.log('window all closed')
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('quit', () => {
  console.log('app quit')
})
```

## 在渲染进程中使用 node

Electron 主进程和渲染进程之间存在安全隔离，如果要在 渲染进程 中使用 node 模块，需要借助预加载文件，通过 webPreferences/preload实现

首先设置 preload：

```js
webPreferences: {
    // nodeIntegration: true,
    // contextIsolation: false
    preload: path.resolve(__dirname, './render/preload.js')
}
```

在 preload.js 文件中写逻辑：

```js
const fs = require('fs')


fs.writeFile("./test.txt", 'some content 2.', () => {
  console.log('done.')
})
```

启动项目，可以看到代码正常执行

## 传递消息

渲染进程 向 主进程 传递消息：

需要使用  contextBridge ，首先在 preload 文件中定义：

```js
const { contextBridge } = require('electron')

// 这里向 window 注入自定义的 myAPI，然后可以向该对象添加数据
contextBridge.exposeInMainWorld('myAPI', {
  platform: process.platform
})
```

此时在渲染进程中可以通过 `window.myAPI.platform` 调用传递的数据



渲染进程与主进程之间通信，使用 ipcMain 和 ipcRenderer，首先在主进程中定义：

```js
ipcMain.handle('send-event', (event, msg) => {
  console.log(msg)
})
```

该方法用于监听 send-event，如果接收到信息，就会打印

在渲染进程中定义：

```js
const { contextBridge, ipcRenderer } = require('electron')

const handleSend = () => {
  ipcRenderer.invoke('send-event', 'some text')
}

// 将 handleSend 挂在 window 
contextBridge.exposeInMainWorld('myAPI', {
  platform: process.platform,
  handleSend
})
```

此时在渲染进程中调用，例如，在一个点击函数中：

```js
document.querySelector('#btn').addEventListener('click', () => {
  myAPI.handleSend()
})
```

## 主进程常用事件

常用的事件有判断 app 是否将要推出、关闭窗体程序、app 失焦等，

例如，判断 app 是否将要退出：

```js
app.on('before-quit', () => {
  console.log('before-quit term.')
})
```

判断 app 是否 失去/获得 焦点的事件：

```js
app.on('browser-window-blur', () => {
  console.log('blur')
})

app.on('browser-window-focus', () => {
  console.log('focus')
})
```

利用这些事件，可以在某些特定触发场景下进行某些处理

## 方法

使用`app.quit()`退出程序，例如实现配合失焦事件关闭 app：

```js
app.on('browser-window-blur', () => {
  console.log('blur')
  setTimeout(() => {
    app.quit()
  }, 3000)
})
```

使用`app.getPath()`方法可以获取一些系统目录，例如：

```js
// 用户桌面目录
console.log(app.getPath('desktop'))
// 音乐目录
console.log(app.getPath('music'))
// 临时文件目录
console.log(app.getPath('temp'))
// 用户数据目录
console.log(app.getPath('userData'))
```

## browserWindow

### 加载完成后显示页面 ready-to-show 事件

使用 loadURL 方式加载某些较慢的页面的时候可以看到明显的白屏，为了防止这一现象，可以设置等待加载页面完成之后再显示

首先设置页面显示为 false：

```js
const win = new BrowserWindow({
    width: 1000,
    height: 600,
    // 页面不显示
    show: false,
})
```

设置页面加载后显示主页面：

```js
win.once('ready-to-show', () => {
    win.show()
})
```

### 父子窗口设置

可以通过多次实例化 browserWindow 来创建多个窗口，例如：

```js
// 第一个窗口
const win = new BrowserWindow({
    height: 300,
    width: 400
})

win.loadURL('http://www.baidu.com')

// 第二个窗口
const win2 = new BrowserWindow({
    height: 300,
    width: 400
})

win2.loadURL('http://www.baidu.com')
```

此时创建的两个窗口没有关系，如果想要设置 win2 窗口为 win 的子窗口，可以在 win2 窗口中设置：

```js
const win2 = new BrowserWindow({
    height: 300,
    width: 400,
    parent: win,
    // 以模态框方式显示 win2 窗口
    modal: true,
})
```

### 声明窗口的属性设置

在声明窗口的时候可以对属性进行设置，例如设置窗口的背景颜色：

```js
const win = new BrowserWindow({
    backgroundColor: '#ccc',
})
```

通过设置`frame`为 true 或 false 来显示或隐藏标题栏

通过设置 x 属性或 y 属性指定窗口在屏幕上的位置

### 保持页面状态

如果对窗口的大小、位置手动修改后，想要在下一次打开 app 时依然保持该状态，可以借助 electron-win-state 实现此功能

安装：

```
npm i electron-win-state
```

使用：

```js
const WinState = require('electron-win-state').default

const winState = new WinState({
    defaultWidth: 800,
    defaultHeight: 600
})

const win = new BrowserWindow({
    ...winState.winOptions,
})

winState.manage(this.win)
```



Tip：出现错误`(node:43636) UnhandledPromiseRejectionWarning: TypeError: WinState is not a constructor`，考虑包是否正确导入，此时打印 WinState，结果为`{ default: [class WinState] }`，需要在导入的语句后面加 default：

```
const WinState = require('electron-win-state').default
```



### 监测浏览器

例如，使用 webContent 打开开发者工具

```js
const wc = win.webContents
wc.openDevTools()
```

监测页面资源加载是否完成：

```js
const wc = win.webContents
wc.on('did-finish-load', () => {
    console.log('finish')
})

wc.on('dom-ready', () => {
    console.log('dom-ready')
})
```

监测是否打开新窗口，使用`new-window`

监测用户是否点击右键：`context-menu`：

```js
wc.on('context-menu', (e, params) => {
    // e.preventDefault()
    console.log('menu')
    // 可以注入 js 代码
    wc.executeJavaScript(`alert('${params.selectionText}')`)
})
```



### 对话框设置

使用对话框需要在主进程中从 electron 中导入：

```js
const {dialog} = require('electron')
```

可以搭配右键点击等使用对话框，例如文件选择对话框

```js
win.webContents.on('context-menu', ()=>{
    dialog.showOpenDialog({
      // 设置选择按钮的文本
      buttonLabel: 'Select',
      // 默认打开的路径
      defaultPath: app.getPath('desktop'),
      // 一些参数设置，例如是否允许多选、创建文件夹等
      properties: ['multiSelections', 'createDirectory', 'openFile', 'openDirectory']
    }).then((result) => {
       // 点击选择按钮之后的逻辑
      console.log(result.filePaths)
    })
})
```

保存对话框：

```js
dialog.showSaveDialog({}).then(result => {
    // 注意这里只是显示对话框 实际的保存需要另写逻辑
    console.log(result.filePath)
})
```

消息对话框：

```js
const answers = ['Yes', 'No', 'Maybe', 'Maybe OK']
dialog.showMessageBox({
    title: 'Message Box',
    message: 'Select one',
    detail: 'message box detail',
    buttons: answers
}).then(({ response }) => {
    console.log(`User selected: ${answers[response]}`)
})
```

### 快捷键设置

globalShortcut 模块可以在操作系统中注册/注销全局快捷键，从而为操作定制快捷键操作：

导入 globalShortcut 模块：

```js
const { app, BrowserWindow, ipcMain, dialog, globalShortcut } = require('electron')
```

注册快捷键：

```js
globalShortcut.register('Ctrl+G', () => {
    dialog.showMessageBox({
        title: 'warning',
        message: 'some test sentences.',
        detail: 'message detail'
    })
})
```

可以通过 unregister 方法注销某个快捷键，例如在按下 ctrl + y 的同时注销上述快捷键：

```js
globalShortcut.register('Ctrl+Y', () => {
    console.log('Press Ctrl + Y')
    globalShortcut.unregister('Ctrl+G')
})
```



## 向 nodemon 添加监听某种类型的文件变化

默认情况下，nodemon 只会监听 js 文件的改变，可以在`package.json`文件中 script 节点下修改，例如：

```json
"scripts": {
    "start": "nodemon --exec electron . --watch ./ --ext .js,.html,.css,.vue"
},
```

（这里使用 npm run start 启动项目）