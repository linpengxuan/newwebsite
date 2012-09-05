---
title: '调整octopress主题小结'
date: 2012-09-05 16:52:34
author: linpx
categories: octopress
comments: true
layout: post
markup: markdown
published: true
---
太难了，对博客主题的更改。这是我大半年以前刚开始写博时的感觉。毕竟octopress不是wordpress，很方便的随便点击几下确定键就可以了。我确实尝试过，失败了。一个很重要的原因是，我不知道css的语法。不知道的话，很简单，就去学。css不是excel啊，太难学了。我的那种抄袭式的学习方式根本派不上用场。在没有办法搞定的前提下，我选择忽略对主题背景修改的重视。这一放，就到了半年后，也就是现在。

我要求的博客样式非常简单。我想让我博客中的背景图片统一。怎么样，很简单吧。问题就在这里，我找到了满意的照片，却不知道应该放到哪个文件夹去。即使放下后，基于octopress完全定制的风格，我也不知道应该从哪儿修改文件。一切从昨天突然想到的一次google收索开始。这篇[博文](
http://melandri.net/2012/02/14/octopress-theme-customization/)
参考后，帮助很大。完全符合我的风格。背景统一，干净，简介。


![](http://farm9.staticflickr.com/8033/7934929014_e330c5a861_z.jpg)

####分离navigation bar

由于octopress基于jekyll模式，我很方便地根据文章的提示，将主题对应的源代码下载下来。没有多想。直接将这个`sass/custom/_styles.scss`考入我的博客文件系统里面。

####统一背景图片

octopress中背景图片是直接存于css中的。更明确点说是，主背景照片为这个文件`source/images/line-tile.png`。在标题框中，我博客的例子就是`sourrabbit
&
linpx`，也存在一个标题背景图片。一般情况下，两张照片是不统一的，不过看起来，我个人感觉不是我想要的风格，我希望的是不会让文字阅读分心的背景图片，同时有些minimalist风格。需要统一。octopress本身文件系统是没有定义标题框背景图片的，仅是对颜色进行了一些过渡，所以我定义标题背景照片为`source/images/header_bg.png`。导入希望用的背景图片，同时命名为上述两个文件名字。

之后我们需要做些调整。我在`sass/custom/_style.scss`的第13~17行中指定了标题框的背景图片。这样直接rake之后，就OK了。

{% gist 3630919 %}

####字体的选择和修改

octopress的中文字体我就不修改了，因为不会。英文字体还是OK的，不过太死板，用的人太多。如果有想换字体的话，可以参考这个[官方文件](
http://octopress.org/docs/theme/)。我直接讲我的方法。

先寻找心仪的字体。我一般上google web fonts里找到后，寻找给出的字体使用链接即可样式如下`<link href='http://
......>`.下面这个文件是我的字体方案，供参考。于`source/_includes/custom/head.html`

{% gist 3633497 %}

找到字体后，就要定义字体如何使用了。于`sass/custom/_fonts.scss`

{% gist 3631051 %}

####背景文件的选择来源

上述的背景和字体选择完了后，octopress看上去一定会清爽很多。我个人的建议是，背景不要用大红大绿，要清淡，简约，不要过于喧宾夺主。因此留给我们的选择不会太多，还好我们有网络。我给出几个网站参考。

1. [Elegant Themes](http://www.elegantthemes.com/demo/?theme=Flexible)
的这个网页非常好，可以调字体，可以选背景pattern，所见即所得;
2. [Ultimate Source for tiled background patterns](
http://www.designshard.com/freebies/ultimate-source-for-tiled-background-patterns/
);
3. [dinpattern](http://www.dinpattern.com/);
4. [patterncooler](http://patterncooler.com
)是我背景下载的网站，牛逼在于可以选择pattern重复的尺寸，颜色，还可以免费下载。东西选择太多，反而不知道要走哪一个了。呵呵。

![](http://farm9.staticflickr.com/8036/7935087262_1cbbd2fa2f_z.jpg)

####结语

前些天我看到了jekyll bootstrap作者整了个[ruhoh.com](http://www.ruhoh.com
),非常漂亮的jekyll博客平台。就不多说它网页上的好处了，居然可以无缝支持我octopress上的中文名permlink这点就让我当时就有移过去的冲动了。只是整起来花些时间，如果是打算新开博客的朋友，倒是一个很好的选择。在机子上或vps上搭好ruby平台就行了。不像octopress这样，当初为了整这个就花了好久。

博客写久了会有写博客的习惯。我和我太太就很有信心将这个博客写上60年。只是未知那时是否还是如此复杂的人机交流模式。
