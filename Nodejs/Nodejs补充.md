# 补充

## 启用 ES6 语法规范

在 `package.json`文件中，添加配置：

```
"type": "Moudle"
```

## npm安装命令的参数

`npm install PACKAGE`：安装到 modules 目录下，不写入任何节点，因此执行 npm install 的时候不会安装该包

`npm install -g PACKAGE`：全局安装，不写入任何节点，因此在执行 npm install 命令时候不会安装该包

`npm install PACKAGE -S`：S 表示 save，此时会将安装的包的名称和版本添加到 dependecies 节点中

`npm install PACKAGE -D`：-D、表示 save-dev，此时会将安装的包名称和版本加入到 devDependencies 中

上述两个参数`-S`和`-D`，主要区别在于是在开发环境中使用还是生产环境中使用：

- `-S`为dep，表示生产环境中使用，例如各种项目插件：element ui、echarts，这些插件在实际程序运行中使用
- `-D`为dev，表示开发环境中使用，例如各种构建工具webpack等，这些用于打包、压缩代码等，程序实际运行中不需要，因此放在 dev 中