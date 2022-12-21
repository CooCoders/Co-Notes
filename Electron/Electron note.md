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

即可使用 nodemon 命令打开 electron 项目