---
layout: post
title: 'Markdown显示数学公式'
description: '使用MathJax来显示'
tags: Markdown
mathjax: true
author: 卫东升
---



## Typora

我的所有博客都是Markdown格式编写的，通过[jekyll](https://jekyllrb.com/)生成静态文件然后放到网上在浏览器上显示。Markdown编辑器用的是[Typora](https://www.typora.io/), 它轻量、跨平台，同时编写博客的时候所见即所得，即当你键入Markdown语法时，它马上会显示预览效果，这一点非常好用。

![typora-introduction](http://dsweiblog.oss-cn-shanghai.aliyuncs.com/2017-12/typora-introduction.jpeg)

在做一些算法题目的时，有时会用到一些数学公式，typora通过设置能够支持这一点。

![](http://dsweiblog.oss-cn-shanghai.aliyuncs.com/2017-12/typora-math-setting.jpeg)

通过在数学公式两端添加**$**符号，可以正确的现实数学公式：

$LaTeX$ 、$a \sqrt b$ 、$\underbrace{a + b \cdots + z}_{26}$

以上是数学公式的**行内**显示；

如果要在**行间**显示，则在头尾分别添加**$$**符号：

$$
LaTeX
$$

$$
a \sqrt b
$$

$$
\underbrace{a + b \cdots + z}_{26}
$$





## MathJax

当我们将上面编写好的数学公式通过jekyll编译城静态页面之后，发现页面显示的是这样的：

![](http://dsweiblog.oss-cn-shanghai.aliyuncs.com/2017-12/jekyll-without-mathjax.jpeg)

原因在于，虽然我们能够在本地编辑器里正常显示数学公式，但由于jekyll在编译解析markdown语法时，缺少对应的插件支持，仍然无法在浏览器里正常显示。

这时候[MathJax](https://www.mathjax.org/)派上用场了！MathJax的就是一个JS的解析数学公式的引擎。它的用法也很简单，直接在页面里引用这个JS文件即可。

```javascript
<script async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```

好了，现在我们引用了这个文件，但发现仍然无法解析数学公式，哪里出了问题啦？

扫描[MathJax文档](http://docs.mathjax.org/en/latest/start.html#tex-and-latex-input)我们发现，MathJax默认情况下解析的是反斜杠(**\\**)符号内的字符而非**$**符号内，我们需要在引用它之前提前声明一下让MathJax解析**$**。配置如下：

```javascript
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [['$$','$$'], ["\\[","\\]"]]
  }
});
</script>
<script async src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```
好了，现在公式能够在线显示啦～
