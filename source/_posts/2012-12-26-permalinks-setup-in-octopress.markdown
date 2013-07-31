---
title: 'permalinks setup in octopress'
date: 2012-12-26 17:00:42
author: linpx
categories: octopress
comments: true
layout: post
markup: markdown
published: true
title: octopress博文设置中英文题目
---
本篇博文专门针对本网站，可能(99.9%)不合适其他octopress博客系统。这里提出仅是抛砖引玉，给octopress爱好者提供一个新想法。

我先介绍我的博客。我的博客发表比较特别，是用电子邮件发送。这意味着只要手里有网络不用搭建任何平台就可以进行文字发表。有兴趣的可以看看我的这篇[文字](
http://colors4.us/blog/2012/01/20/%E5%88%A9%E7%94%A8%E9%82%AE%E4%BB%B6%E5%8F%91%E8%A1%A8octopress%E5%8D%9A%E5%AE%A2/)
，**介绍如何用电子邮件发布博客**。

我也不知道是如何整好utf-8代码的，但弄好后，发布的博文地址全部带上了中文。在浏览器上还是挺好看的，不过拷贝后就能看到很多如`%88%A9%E7%94%A8%`般巨长地址，看完总是让我留下**这不科学**的惆怅感。不过今天在我进行调试的时候，总算摸到**一个让博文保持中文标题，同时网址走英文格式的方法**不敢独自敝帚。

在邮件标题栏中，我发表原来是这样书写的：

民国101年台湾旅行小记 || secret: ****** / author: linpx / categories: life /
comments: true，

(其实就是YMAL的文件头的设定), 现在如果改成这个样式就完全没有问题：

trip to Taiwan in 2012 || title: 民国101年台湾旅行小记 / secret: ****** / author:
linpx / categories: life / comments: true

这样我就得到一片中文博客的题目，和一个英文的自定义网址，可以看成是permalink，方便以后转到其他平台。我果然已经在开始想其他平台了。

参考了如下网址：

1. [Permalinks wiki](https://github.com/mojombo/jekyll/wiki/Permalinks)
2. [Permalinks Jekyll configuration](
https://github.com/mojombo/jekyll/wiki/Configuration)

图片一看就明白我说的是什么了。(文字表达能力不好的极好表现)

![]( http://farm9.staticflickr.com/8499/8309147407_eb75fc9525_b.jpg)
