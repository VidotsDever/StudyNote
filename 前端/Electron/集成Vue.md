* 安装Vue命令行工具：

```
npm install -g @vue/cli
```

* 创建工程脚手架：

```
vue create integrate-vue
```

* 安装Electron：

```
npm i -D electron
```

* 在工程根目录创建main.js文件，并在package.json文件将其配置为Electron入口文件：

```
"main": "main.js",
```

* 修改package.json文件的脚本：

```
"serve": "vue-cli-service serve",    
"start": "electron .",
```

* `npm run build`产生的目录为`dist`，所以修改win.loadURL的参数：

```
win.loadURL(`file://${__dirname}/dist/index.html`);
```

* 配置相对路径在Electron奏效的方法：

```
在根目录下创建vue.config.js文件，添加如下内容：

module.exports = {
    publicPath: './'
}
```

