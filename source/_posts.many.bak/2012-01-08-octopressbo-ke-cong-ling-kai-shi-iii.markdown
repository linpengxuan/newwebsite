---
layout: post
title: "Octopress博客从零开始 III"
date: 2012-01-08 08:22
comments: true
author: linpx
categories: octopress computer
---
其实现在已经可以开博了，不过如果进行一些个性化博客格式的调整，也就会让你自己的页面出彩些，对我来说，就是有些小爽了。<!--more-->

####博客标题字体
进入octopuses下的目录，修改文件`/source/_includes/custom/head.html`。从[Google webfonts]()http://google.com/webfonts)往里头添加自己喜欢的字体。这是我的范例。{% codeblock %}
echo "<link href='http://fonts.googleapis.com/css?family=Ruge+Boogie|Ruthie|Flavors|Rancho|Galdeano' rel='stylesheet' type='text/css'>" >> source/_includes/custom/head.html
vi sass/custom/_font.scss #这里修改主题，使用刚才添加的字体。我的如下
$header-title-font-family: "Flavors", sans-serif;
$header-subtitle-font-family: "Galdeano", sans-serif;
vi _config.yml #这里修改博客标题的内容，我的如下
url: http://colors4.us
title: sourrabbit & linpx
subtitle: a blog for the colors in our life
author: Dong Ping; Lin Pengxuan
rake generate;rake deploy #看看效果
{% endcodeblock %}
####我的RSS图标呢？
打开页面会发现RSS图标不见了，事实上一搜页面也能找到，就是图片无法显示，原因是我们更改了blog目录后，images文件夹没有相应变化。调整很容易，进入`config.rb`文件，修改如下：
{% codeblock %}
http_images_path = "/images"
css_dir = "public/stylesheets"
{% endcodeblock %}
####颜色调整
可以参考这篇[文章](http://blog.bigdinosaur.org/changing-octopresss-header/),我仅修改了标题的颜色等，修改`sass/custom/_colors.scss`如下:{% codeblock %}$$header-bg: #f2f2f2;
$title-color: #000000;
$subtitle-color: #000000;
{% endcodeblock %}
####如何continue to read
有的时候文章太长了，不想把所有的都一次性放在网页上，这时候只需在想分割出来的段落后放置一个`<!--more-->`就可以了
####博客的sidebar
sidebar可以在`_config.yml`中进行基本的设置，我的如下{% codeblock %}
twitter_user: linpx
google_plus_one: true
disqus_short_name: linpx
post_asides: [ asides/recent_posts.html, asides/twitter.html] ＃这条是控制当我们看博客文章时呈现的sidebar形式，没有的话，走default
{% endcodeblock %}
	
我们也可以修改footer，比如把`Powered by Octopress`改成`Proudly Powered by Octopress`,可在这个文件上修改`source/_includes/custom/footer.html`
####header调整
我对header调整没有任何概念，直接从这哥们的[博客](http://blog.bigdinosaur.org/changing-octopresss-header/)上抄下来。下面的这段代码放入`sass/custom/_layout.scss`可以产生我博客上面的效果，请参考。
{% codeblock %}
$max-width: 1350px;
body > header h1 {
      padding-left:2.5em;
      text-align:right;
      @media only screen and (min-width: 432px) {
              text-align:left;
      }
      @media only screen and (min-width: 768px) {
              padding-left:3em;
      }
      @media only screen and (min-width: 992px) {
              padding-left:2em;
      }
}

body > header h2 {
      padding-left:5.62em;
      text-align:right;
      @media only screen and (min-width: 432px) {
              text-align:left;
              padding-left:3.9em;
      }
      @media only screen and (min-width: 768px) {
              padding-left:5em;
      }
      @media only screen and (min-width: 992px) {
              padding-left:2.9em;
      }
}

body > header h1:before {
      content:"";
      position:absolute;
      left:0em;
      right:0;
      top:1.5em;
      height:110px;
      width:110px;
      overflow:hidden;
      text-align:right;
      background-image:url('http://farm8.staticflickr.com/7004/6658205771_3256ae9101_m.jpg');
      background-repeat:no-repeat;
      @media only screen and (min-width: 432px) {
              top:.32em;
      }
      @media only screen and (min-width: 768px) {
              left:.75em;
      }
}
{% endcodeblock %}

在sidebar上介绍关于这个博客的内容，比如`about me`等，可以进行如下操作{% codeblock %}
cd source/_includes/custom/asides
vi about.html #必须用html来写，不会的话，可以到daring fireball上套用那儿的markdown转换html功能
<section>
  <h1>About us</h1>
<p>写你自己想要放入的介绍内容life.<a href="http://www.colors4.us/about-us/index.html">...</a></p>
</section>
cp source/_includes/custom/asides/about.html source/_includes/asides/about.html #把文件考入asides的文件夹
rake new_page["About us"]
vi source/_includes/custom/navigation.html #添加以下内容
<li><a href="{{ root_url }}/about-us">About</a></li>
vi _config.yml #修改_config.yml在default asides中加入如下`asides/about.html` 
{% endcodeblock %}至此博客sidebar上就多了about us的介绍，同时结尾处提供了一个链接，可以转到navibar上about的页面，方便做更详细的介绍。

####disqus评论导入
从04年开始写的博客看得人虽然不多，评论几年下来几百条也是有的。换到octopress来，能保留还是要尽量保留。但是太多的情况是，网上遍地的disqus导入都涉及到地址变换，这个对我挑战过大。有次上网时，看到[@laoyang945](http://twitter.com/laoyang945)提出的方法很靠谱。首先保证wordpress上的博客文章可以导出到disqus上，这个没问题的，有插件的。然后在disqus登录后，在tools中选中`Migrate Threads`的`Upload a URL map`,我们会得到一个csv文件，第一个单元格是登记于disqus的地址，我们在第二单元格填入想要链接的博客地址就可以了。这样做的好处是，以后再次迁移的时候，评论可以很容易的管理，缺点是得一条一条地整理。
####wordpress导入
对wordpress导入我是最无语的。我曾经导出成功过，可是之后要再次实行的时候，却失败了。目前对我无解，不过我个人推荐这个[exitwp](https://github.com/thomasf/exitwp)，大伙应该可以导出的吧。要注意看下面`getting started`的提示，如果提示dependancy有问题的话，根据提示去安装，要么用`python`安装，要么直接将py后缀的文件放到文件夹内。
####tag_cloud
很遗憾，这个目前对我无解，我尝试了如下方法：
	
* [Tag Clouds With Octopress](http://aijazansari.com/2012/01/07/tag-clouds-with-octopress/)

* [Tag Cloud Git](https://github.com/imathis/octopress/pull/282)

####favicon
网站的小图标也是可以留意的，有的人说只有这个也ok了，网站才算叫just right. favicon的更新方法很简单，将你中意的`favicon.png`这个文件考入`source`文件夹，在rake更新即可。
####关于octopress的备份
我对git的了解基本属于残疾人，连入门都不算。作为一个从windows95就开始折腾的中国网民来说，装机和备份是生存手段。这里我介绍一个octopress的备份。我们知道，博客就两项内容，一个是内容，一个是格式。内容根本没有任何问题都是markdown格式的，稍微拷下就ok了。格式咋办了，写到这里都快崩溃了，再做一次肯定不干，直接来个tar吧。用下面的命令备份到目前为止的文件夹吧，以后恢复时要做的是装好octopress后，用这个覆盖就可以了，没试过，我觉得ok就是了。{% codeblock %}
tar -zcvf blogbak.tar.gz /home/wwwroot/blog #这是压缩命令
tar -zxvf blogbak.tar.gz #这是解压命令
{% endcodeblock %}
####自动完成文件名的小技巧
这个很多人应该都知道，我到这个礼拜才从同事那儿学会，就是在敲入文件地址的时候，当输入的头几个字母已经足以将该文件和其他文件区分开来时，按`tab`键会自动补齐。这同样适用于文件夹名字的输入。
####TextExpander的使用
Octopress虽然号称是黑客博客工具，但是有时候用图形化界面来整理感觉也很不错。这里从网上借鉴[Quickie: TextExpander and Octopress](http://angelostavrow.com/blog/2011/12/11/quickie-textexpander-and-octopress/)，(这下看出我标题上图案是从哪抄来的吧，-_-!!)得出如下心得。[TextExpander](http://www.smilesoftware.com/TextExpander/)的作用是任何在mac上的键幅输入都会被跟踪，当跟踪到数据和预设的一致时，代入对应字符串。比如我输入预设的`sshi`时，mac上就会自动变成`ssh root@x.x.x.x`。提几个看法。

*	可以设`rknp`为`cd ~/blog; rake new_post["%fill:title%"]; vi ./source/_posts/%Y-%m-%d-*.markdown`。
*	可以设`rkgr`为`cd ~/blog; rake generate; rake deploy`
*	其实发挥想象力的话，你可以在vps服务器上设立dropbox文件夹，把预设`public`置入同步文件夹内，我们在本地上用Mou或Byword书写，本地Dropbox存盘，本地rake generate。然后那儿就直接被更新了。这个只是想法，还没有被测试过，以后来整。

####给网站添加访问分析 Google Analytics
我在看了这个[网页](http://www.whatwherewhy.me/colophon/)后，发现我还可以添加网站分析，毕竟是自己努力出来的东西，看看大伙是否有兴趣访问还是不错的，不过很多情况是远逊于预期。:)

1. 先于[Google Analytics](https://www.google.com/analytics)开通和自己网站相关的服务，比如登记自己的网址，取得GA的Track ID，应该如该样式`UA-28584XXX-X`.
1. 修改`_config.yml`最后部分，将ID置于`google_analytics_tracking_id:`项目之后，rake一下就行了。
1. 之后进入GA网站看report吧，你就会知道何时多少人访问过你的网站。
