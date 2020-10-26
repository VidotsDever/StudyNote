####  修改Jupyter的默认位置

* 初始化生成配置文件

```
jupyter notebook --generate-config
```



* 修改配置文件

```
在配置文件里找到 c.NotebookApp.notebook_dir配置项，去掉注释，并且设置目标目录(必须提前创建好)
c.NotebookApp.notebook_dir = 'D:\jupyter_note'
```



上面配置完后，只能通过Anaconda控制台输入命令`jupyter notebook`启动才能生效。



#### 为Jupyter添加代码提示功能



```
pip install jupyter_contrib_nbextensions

jupyter contrib nbextension install  --user

pip install --user jupyter_nbextensions_configurator

jupyter nbextensions_configurator enable --user

重启Jupter Notebook，打开里面的Nbextensions，勾选Hinteriand。
```

