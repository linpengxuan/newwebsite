---
title: '在octopress上引入相片流'
date: 2012-11-10 22:53:26
author: linpx
categories: octopress computer
comments: true
layout: post
markup: markdown
published: true
---
我一直很想在博客上能够多一项小功能，就是照片流。这样我就可以把flickr上的照片一连串的全部共享出来，或者有次出行了，拍了些照片，只要把博文地址微博给朋友，就可以上来浏览了。没错，就是和以前msn博客上的相册是一个道理。只是换到了octopress后，这些都得自己动手整。很繁琐，不过弄好了的话，还是有成就感的。

####设立相册的可能性

当然是可能的了，要不这篇博客就没有意义了。

我本人是不会任何编程，也不是学计算机的，所以很多时候，我知道这样去尝试可能可以，至于要问我为什么可以，对不起，实际上我这里给出的编码啥意思，自己也不明白。只要能得到想要的结果，知其然而不知其所以然，也挺好，不是？

我这里引用的代码是[fancybox](http://fancyapps.com/fancybox/),做好后会有这样的[效果](http://fancyapps.com/fancybox/demo/)。很显然，对于一个超小众的octopress博客平台来说，想使用另一个超小众的的相片流代码，一定得找成功的先例。实际上，平台比较出名的[slash主题](http://zespia.tw/Octopress-Theme-Slash/)已经支持了fancybox，不过并未给出如何设置。于是，我找到了这篇文章[Integrating Photos Into OctoPress Using FancyBox and Plugin](http://tritarget.org/blog/2012/05/07/integrating-photos-into-octopress-using-fancybox-and-plugin/)。写得非常好，以至于我在第一次rake generate就成功了，而并未出现我期待的解析错误。

####操作步骤

我这里就直接给从操作步骤了，想要理解为何的同学，还是移步到[引文链接](http://tritarget.org/blog/2012/05/07/integrating-photos-into-octopress-using-fancybox-and-plugin/)去看看吧。

**第一步**，把'photos_tag.rb'[文件](https://gist.github.com/2631877)放到`plugins/`文件夹去。
{% codeblock %}
git clone git://gist.github.com/2631877.git  #文件在2631877文件夹中
{% endcodeblock %}

**第二步**，在`source/_includes/custom/head.html` 文件底部加入如下内容
{% codeblock %}
<!-- Load jQuery -->
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js" type="text/javascript"></script>
<script type="text/javascript">
    jQuery.noConflict(); // ender.js conflicts with jQuery
</script>

<!-- Load FancyBox -->
<link rel="stylesheet" href="/fancybox/jquery.fancybox.css" />
<script src="/fancybox/jquery.fancybox.pack.js" type="text/javascript"></script>

{% fancyboxstylefix %}

<!-- Custom Scripts -->
<script language="Javascript" type="text/javascript">
    // ender.js gobbles jQuery's ready event: Use ender.js $ instead
    $(document).ready(function() {
        jQuery(".fancybox").fancybox();
    });
</script>
{% endcodeblock %}

**第三步**， 在`sass/custom/_styles.scss`追加如下内容
{% codeblock %}
/* FancyBox Galleries */
$rad: 6px;
ul.gallery {
  border: thin solid gray;
  -webkit-border-radius: $rad;
  -moz-border-radius: $rad;
  border-radius: $rad;
  text-align: center;
  padding: 5px;
  li {
    display: inline;
    padding: 5px;
  }
}
{% endcodeblock %}

**第四步**，从这里开始就是微调了。如果看过原文的话，会发现相册流是不直接支持flickr的网址的，任何网址在使用时，都会在前方默认加上网站地址，我的情况是，会出现诸如`http://colors4.us/farm9.staticflickr.com... `等没有意义的网址。这一步的解决方法，对我来说可以用，但不完美，我个人妥协了。可以在`_config.yml`中加入如下内容：
{% codeblock %}
photos_prefix: https://farm9.staticflickr.com/
{% endcodeblock %}
这样做就如同设立了歌permlink一样，规定死了打头网址的内容，后面我们在博文中输入相册照片地址`http://farm9.staticflickr.com/8198/8171602168_1788957e3c_m.jpg `时，只需输入`8198/8171602168_1788957e3c_m.jpg `即可。

但是如果常用flickr的朋友就会发现，这里有个前提，就是所有的相片的网址，都必须在`farm9`这个网址段上。我个人来说，使用时间够长，我照片网址从`farm1`到`farm9`都有，设死了的话就没有办法套用其他网址段的照片了。我做了很多次试验，发现只要后面那段数字正确，前面的网址段变化的数字不会产生影响，或者直接说`http://farm9.staticflickr.com/8198/8171602168_1788957e3c_m.jpg `等于`http://farm1.staticflickr.com/8198/8171602168_1788957e3c_m.jpg `。

**第五步**，这里介绍实例，请一定注意看井号后面的说明。如果只展示一张的话，可在博文中插入如下内容：
{% codeblock %}
[% photo 8198/8171602168_1788957e3c_b.jpg default 这里输入照片的名字 %] #把句子前后的中括号改成大括号，因为它本身放入博文就可执行了
{% endcodeblock %}

如果展示相册流的话，则插入如下内容：
{% codeblock %}
[% gallery %] #把本行句子前后的中括号改成大括号
7120/7698203656_ae8ae99ed3_b.jpg: pic1      #thumb pic默认为7698203656_ae8ae99ed3_m.jpg，刚好和flickr对应
3504/3997360729_cdaf975b63_z.jpg[3504/3997360729_cdaf975b63_m.jpg]: pic2       #可以自己设定thumb pic置于中括号内
6182/6150404978_10c5d6b9c5_b.jpg: pic3
7278/7714113508_3c28b8eaa0_b.jpg:                      #可以不起名字，但是一定不能省略冒号
2511/3976228983_a670a644b4_o.jpg[2511/3976228983_5ef14d2ca5_m.jpg]: pic5
4142/4782988086_065998b9af_b.jpg: pic6
[% endgallery %] #把本行句子前后的中括号改成大括号
{% endcodeblock %}

####演示

我这里就先演示一下fancybox相片流的效果吧。

{% photo 8198/8171602168_1788957e3c_b.jpg default 这里输入照片的名字 %}

---
{% gallery %}
7120/7698203656_ae8ae99ed3_b.jpg: 这里起名字
3504/3997360729_cdaf975b63_z.jpg[3504/3997360729_cdaf975b63_m.jpg]: 也可以不起名字
6182/6150404978_10c5d6b9c5_b.jpg: 起个喜欢的名字吧
7278/7714113508_3c28b8eaa0_b.jpg:  
2511/3976228983_a670a644b4_o.jpg[2511/3976228983_5ef14d2ca5_m.jpg]: maybe an English one?
4142/4782988086_065998b9af_b.jpg: haha
{% endgallery %}



我平常拍照不多，fancybox简单的功能刚好能满足我的需求。呵呵，不容易啊，上了octopress的船要想下，确实不容易啊，只能自己举着鞭子敲着自己了。:)