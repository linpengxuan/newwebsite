---
title: '韩版三星Galaxy S2固件升级记'
date: 2012-07-29 21:37:41
author: linpx
categories: computer
comments: true
layout: post
markup: markdown
published: true
---
####前因
iPhone4s用久了，有些疲劳。刚好手中有部去年购入的Samsung Galaxy
SII，就打算换着用上一段时间。这部安卓手机的固件还是2.3.4，捉摸着把固件给升了再使用。这一捉摸就是一礼拜。

网上不是说了吗？苹果手机都等着越狱，安卓手机都等着刷固件。这话通俗得已经开始暗示刷机的容易了。于是，我就上了机锋论坛。到那就傻眼了，整屏整屏的固件，我都不知道该如何下手。更搞的是，他们给出了固件，却没有明确给出相应固件的使用方法，原谅我的这个评论，这是我当时的想法。作为08年末就开始使用摩托milestone的用户来看，刷固件的前提是保证不变砖，很遗憾，在很多帖子上，我无法确定这个地方。

照理，既然国内论坛不明确，我就直接上最新的Cyanogenmod
9固件。稍微搜索了下，结果也碰壁了，我这款型号叫SHW-M250K的韩版盖世兔还相当搞。我简单列几点：

*. 要用最新的CM9固件，先得升到ICS.这不是扯淡吗？我就是为了升这个才来的；

*. Official CyanogenMod support is for ONLY for the International
Version(GT-I9100), Bell Galaxy S II (GT-I9100M), and Telcel Galaxy S II
(GT-I9100T), NOT any other Samsung Galaxy S II
variant.这句话一上来我就不踏实，到底会不会把韩版变砖头呢？

后来一横心，变砖就变砖吧。即使这样，在恢复模式中还是无法刷入固件。

####解决

到解决的这一步时，我已经杀死无数的脑细胞。手机因为刷固件次数过多，已经用着一个不知从何处整来的固件。如果不能顺利升入版本，我貌似也就没有再使用的冲动，所以必须上CM9。我在中英文论坛上无法解决的问题，在稍微进行下头脑风暴就觉得能在韩国论坛上找到答案。果然。下面所说的升固件的手段就是从[韩国博客](
http://lhc368.blog.me/60160952763)上考下来的。作为韩版升级CM固件，是个很好的参考。

我先说思路。恢复模式中升级CM固件无法成功的原因时没有通过认证，韩版的型号是SHW-M250K，CM固件可用的所有的型号均是I9100或I9100T等变体。我们需要做的就是将下载的固件型号在里面的某些类似于ini文件中修改即可。

需要如下文件:

*. 韩版手机一台，型号为SHW-M250K/S/L;

*. [此处下的CM9 nightly固件](
http://download.cyanogenmod.com/?device=galaxys2&type=nightly);

*. [此处下载AcroEdit](http://www.acrosoft.pe.kr/board/60677)用于修改固件对应的手机型号;

方法如下：

1.
用压缩文件打开固件文件，提取固件(我下载的为:cm-9-20120728-NIGHTLY-galaxy2.zip)中的`/META-INF/com/google/android/`中的updater-script和`system`中的build.prop文件置于桌面。
2.
用AcroEdit分别打开两个文件，将其中的GT-I9100*全部换成SHW-M250K.一共替换十处。换好后，在AcroEdit中存盘，将两个文件通过压缩程序放回原位置，覆盖原有的文件。
![](http://farm8.staticflickr.com/7260/7668410176_9bfdd5d4be.jpg)
![](http://farm9.staticflickr.com/8424/7668410222_8906981ab7_z.jpg)
![](http://farm9.staticflickr.com/8022/7668410804_b5fbba1672_b.jpg)
3.
然后将该文件考入手机SD卡根目录，就可以正常的通过恢复模式刷固件了。三星进入恢复模式的方式为：音量键+Home键+开关。进入后，选择`install
zip from sdcard`，选好刚才考入的zip固件就可以update。完了记得来个factory reset就没问题了。

####注意

*. 只要是刷机就有风险，三星的质保肯定就没有的了。

*. 这次刷机完之后，下次再刷其他固件就不用再改了，机子实质上已经变成了一台无锁的国际版。也就是随便刷。
