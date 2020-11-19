**注意**：如果浏览器渲染出来的是模板代码，则需要安装`npm install hexo-renderer-swig`；

#### 如何添加菜单项

Hexo NexT主题下默认有首页和归档两个菜单，我们还可以开启其他菜单项，比如分类、标签、关于等。

首先打开主题下的配置文件`_config.yml`，然后搜索`menu`找到如下配置项，将`about`、`tags`、`categories`前的`#`号去掉，就开启了关于、标签和分类标签，当然还有其他菜单项也可以开启。

注意：`/`和`||`之间的空格要去掉，否则访问会出现问题。



```
menu:
  home: /|| home
  tags: /tags/|| tags
  categories: /categories/|| th
  archives: /archives/|| archive
```



接着需要新建相关的页面：

```
hexo new page "tags"
hexo new page "categories"
```

在产生的`source/categories/index.md`和`source/tags/index.md`分别添加

```
title: 分类
date: 2020-11-15 16:14:11
type: "categories"

title: 标签
date: 2020-11-15 16:14:02
type: "tags"
```



