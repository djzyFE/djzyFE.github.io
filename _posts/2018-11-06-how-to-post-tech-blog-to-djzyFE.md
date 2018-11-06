---
layout: post 
title: 如何在djzyFE撰写并提交博文
author: 卫东升
tags: 写作
---

# 如何在djzyFE撰写并提交博文

> [djzyFE](https://github.com/djzyFE/djzyFE.github.io)博客部署在[Github](https://github.com)上，当前域名为[djzyfe.github.io](https://djzyfe.github.io)。



博客基于[Jekyll](https://www.jekyll.com.cn), Jekyll是一个轻量级的静态网站生成框架。它能自动将Markdown文档转化为静态页面。因此熟悉简单的Markdown就能玩转Jekyll。

## 安装运行本地Jekyll系统

### 下载Jekyll
```shell
gem install jekyll
```
如果下载慢，可以尝试替换国内gem源。


### clone项目
```
git clone git@github.com:djzyFE/djzyFE.github.io.git
```

### 编译运行
运行`jekyll build` 或者`jekyll build --watch`,生成`_site`目录，博客的内容都会生成在这个目录内。

如果想本地预览，运行`jekyll serve` 或`jekyll serve --watch`, 浏览器访问127.0.0.1:4000。



## 博文结构

### 文档命名
项目中有一个`_posts`的目录，里面存放所有的博文。

每个文档的**必须**以如下形式命名，否则Jekyll无法识别：

```markdown
yyyy-mm-dd-xxxxxxxxxxx.md
```

这里`yyyy-mm-dd`是编写文档的日期，例如`2018-11-06`，这个日期会显示在网站页面上。

`xxxxxxxxxxx`是文档的标题，主要是英文标题，单词和单词之间可以用`-`进行连接。



### 博文格式

博文头部需包含如下内容：

```yaml
---
layout: post 
title: 如何撰写博客 //举例
author: 北极光 //举例
tags: 写作 //举例
---
```
还有一些其他参数，但不是必须的，这里仅列出一定需要的参数。


