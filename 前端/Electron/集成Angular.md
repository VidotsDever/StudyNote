* 安装Angular命令行工具：

```
npm i -g @angular/cli@latest
```

* 创建Angular工程脚手架：

```
ng new integrate-angular
```

* 修改`src/index.html`文件：

```
<base href="./" />
使得所有的资源路径都是相对于index.html文件；
```

* 安装Electron库到工程中：

```
npm i electron -D
```

* 创建`main.js`文件，并将其注册到`package.json`中，使其作为Electron的入口文件：

```
"main": "main.js"

main.js文件内容与前面的Electron入口文件内容相同；唯一特殊之处在于加载HTML文件。

1. 首先使用npm run build编译Angular工程，查看编译好的HTML文件目录
2. 一般来说：win.loadURL(`file://${__dirname}/dist/工程名称/index.html`)
```

* 修改`package.json`的脚本，启动应用：`npm run build`、`npm run start`

```
"scripts": {
    "ng": "ng",
    "serve": "ng serve",
    "start": "electron .",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
}
```

* 配置实时加载：开发时，让Electron加载`ng serve`启动的地址，而不是`ng build`构建的文件。

```
win.loadURL(`http://localhost:4200`)

打开两个命令行窗口：
npm run serve
npm run start
```

* 构建生产环境，在package.json中添加脚本：

```
"build.prod": "ng build --prod --baseHref=./",
```

