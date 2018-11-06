本文主要介绍如何撰写博客并进行提交。

[djzyFE](https://github.com/djzyFE/djzyFE.github.io)博客部署在[Github](https://github.com)上，当前域名为[djzyfe.github.io](https://djzyfe.github.io)。



本博客基于[Jekyll](https://www.jekyll.com.cn)进行搭建。[Jekyll](https://www.jekyll.com.cn)是一个轻量级的静态网站生成框架。[Jekyll](https://www.jekyll.com.cn)会自动将markdown文档转化为静态页面展示。因此，熟悉简单的markdown玩转[Jekyll](https://www.jekyll.com.cn)。另外，[Jekyll](https://www.jekyll.com.cn)的主题样式非常丰富，同时熟悉简单的HTML/CSS就能实现样式的自定义。


因此首先需要搭建本地Jekyll环境。

```shell
gem install jekyll
```

如果下载慢，可以尝试替换gem源，具体可以google。



`clone`项目源码：

```shell
git clone git@github.com:djzyFE/djzyFE.github.io.git
```



撰写博文

`clone`完之后，我们发现目录下有一个`_posts`的目录，里面存放所有的博文。

每个文档的**必须**以如下形式命名，否则Jekyll无法识别：

```markdown
yyyy-mm-dd-xxxxxxxxxxx.md
```

这里`yyyy-mm-dd`是编写文档的日期，例如`2018-11-06`，这个日期会显示在网站页面上。

`xxxxxxxxxxx`是文档的标题，主要是英文标题，单词和单词之间可以用`-`进行连接。



博文格式

博文头部需包含如下内容：

```yaml
---
layout: post 
title: 如何撰写博客
author: 卫东升
tags: 写作
---
```

