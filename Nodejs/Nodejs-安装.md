# Node.js Note

## 安装

建议安装LTS版本（长期支持版），然后直接双击安装即可。

测试 Node.js 安装的结果：查看版本

```
node -v
npm -v
```



## 配置

### 改变包默认的下载位置

下载完默认的包下载位置是在C盘，使用命令：

```
npm root -g
```

查看nodejs下载依赖包的默认路径，可以修改这个路径：

首先在安装路径下新建两个目录：`node_global` 和 `node_cache`，使用命令将下载指向创建的文件夹（一般在安装目录下创建 `node_global` 和 `node_cache` 文件夹）

```
npm config set prefix "C:\Program1\Nodejs\node_global"
npm config set cache "C:\Program1\Nodejs\node_cache"
```

查看npm全局模块的存放路径：

```
npm get prefix
```

查看npm缓存默认存放路径：

```
npm get cache
```

注意，改变默认下载全局与缓存目录后，windows下需要添加环境变量，如果添加环境变量后一些命令，例如 pnpm 仍然无法运行，可能需要重启后才生效

### 改变下载源

默认下载源在海外，因此下载模块速度太慢，可以改变为国内的镜像。

查看默认的镜像下载地址：

```
npm config get registry
```

使用命令将下载源指向淘宝源

```
npm config set registry https://registry.npm.taobao.org/
```



安装cnpm：（提高软件安装速度）：

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

安装结果的模块可以直接在 node_global 文件夹下查看

### 安装与基础配置参考

整个过程可以参考：

```
https://blog.csdn.net/qq_42006801/article/details/124830995
```





## 问题

### 没有权限创建文件夹

使用 npm 安装模块的时候，如果 node.js 安装在C盘，容易出现没有权限的问题，使用 npm 命令之后，显示

```
Error: EPERM: operation not permitted, mkdir ....
```

需要手动为安装目录赋权限，可以参考：

```
https://blog.csdn.net/m0_59562614/article/details/125464108
```

赋权限的过程可能会出现：

```
将安全信息应用到以下对象时发生错误，拒绝访问
```

此时有可能是手动创建的 `node_global` 和 `node_cache`文件夹的权限设置问题，分别对这两个文件右键->属性->高级->编辑->找到“完全控制”选项并勾选，然后修改整个 node 文件夹的权限即可

参考博客解决：

```
https://blog.csdn.net/qq_37653414/article/details/103462173
```

如果还是有WARN信息，可以**直接对文件夹下的`node_global`和`node_cache`文件夹赋完全控制的权限**



### 卸载相关

如果出现问题，需要卸载，则需要删除干净，比较隐藏的文件或文件夹为：

- User目录下的 `.npmrc`文件
- User/AppData下的npm相关文件夹（npm，npm-cache）



### 去除 npm 命令的 warn 信息

使用 npm 命令时，一直有warn信息：

```
npm WARN config global `--global`, `--local` are deprecated. Use `--location
```

解决方法为：在安装路径中找到 `npm`和`npm.cmd`文件（可能在文件夹内部无法编辑，需要复制到文件夹外），然后将`prefix -g`替换为 `prefix --location=global`



## Linux Node.js 安装

官网下载并上传文件：`node-v8.12.0-linux-x64.tar.xz`

解压并修改文件名：

```
tar -xvf node-v8.12.0-linux-x64.tar.xz

mv node-v8.12.0-linux-x64 nodejs
```

此时，进入node\bin目录下，运行命令 `./node -v`应该可以查看 node 的版本信息

全局使用设置：为 node 和 npm 两个命令设置软链接 	

```
ln -s /usr/nodejs12/nodejs/bin/node /usr/local/bin/node  
ln -s /usr/nodejs12/nodejs/bin/npm /usr/local/bin/npm
```

设置环境i比那辆：

```
vim /etc/profile
```

末尾添加：注意路径

```
export PATH=$PATH:/opt/nodejs/node-v16.13.1-linux-x64/bin
```

立即生效：

```
source /etc/profile
```

使用 `node -v`查看结果