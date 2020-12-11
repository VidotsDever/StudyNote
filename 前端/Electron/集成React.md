* 创建React脚手架：

```
npx create-react-app integrate-react
```

* 安装Electron：

```
npm i -D electron
```

* 在package.json添加Electron的入口文件，并修改脚本：

```
"main": "main.js"

"serve": "react-scripts start",    
"start": "electron .",
```

* 设置应用的基础路径，以`index.html`为相对路径，在package.json文件添加：

````
"homepage": ".",
````

*  修改main.js中的index.html文件加载路径，因为React打包的路径在build目录中：

```
win.loadURL(`file://${__dirname}/build/index.html`)
```

*  启动应用：

```
npm run build
npm run start
```

* 实时热加载：`npm run serve`、``

```
修改win.loadURL的路径：
win.loadURL(`http://localhost:3000`)
```

























