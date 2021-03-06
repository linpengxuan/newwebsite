---
title: 'Asus RT-AC66U开箱，使用，把玩（二）'
date: 2012-12-08 14:32:42
author: linpx
categories: computer
comments: true
layout: post
markup: markdown
published: true
---
网购的[Asus RT-AC66U](http://www.asus.com/Networks/Wireless_Routers/RTAC66U/)
总算在热切的期盼中到达了。花钱和浪费时间一样，负疚和犯罪感一样会同时出现。刚开始只是想搭车黑五，然后就出手了台比在韩国手机套餐还便宜的x230，之后考虑家里的网速，就问自己是不是再出手个路由器。你看，我就是这样一步步掉入这样商业社会的陷阱中去的，不要学啊。

![](http://farm9.staticflickr.com/8503/8254405262_8cd4a943e4_z.jpg)

也可能是我要求不多，用下来感觉这个路由器实在是过于强大了。原本还打算开着原来的老笔记本做个NAS，现在连这个都省了。用路由器挂着硬盘来下载。速度也很满意。<!--more-->

1. 双频2,4Ghz和5Ghz是可以同时开启。5Ghz的频道，我手里的Galaxy S3和林太的iPad
mini都可以连的上，speedtest的速度因为干扰少的原因，表现很好；
2. 诚如包装盒所述，video
streaming能力极强。一天多用下来，在家里的网内登录路由器上的USB硬盘的电影，完全没有停顿。甚至下载过程当中，直接就可以看已下载好的部分。
3. 外形漂亮。确实漂亮。
4.ASUS的软件支援很到位。支持安卓和iOS系统的APP。我用手机的4G网络都可以远程登录我的路由器，通过APP可以分享出USB硬盘文件的链接，对，就如dropbox的public文件夹一般，唯一区别在于大小无限制。ASUS起了个不错的名字,Aicloud.
5. 扩展性不错。当然，想TOMATO，dd-wrt还是没有支持，不过我通过安装原生系统修改的固件[asuswrt-merlin](https://github.com/RMerl/asuswrt-merlin/wiki/About-Asuswrt),安装花了我不少心思。下文会详述。

![](http://farm9.staticflickr.com/8061/8253331499_16957f4bb1_z.jpg)

####路由器调整

1.在前述的asuswrt-merlin地址上下载最新的固件，用和正常的华硕升固件的方式刷入即可。据作者介绍，该固件开发时，也得到华硕官方的帮助，他觉得华硕在这上面做的很棒。
2. 我比较过路由器在设置了密码前后的速度区别。还是显著到我需要烦恼的地步，还是沿用了原来的老方法，不设密码，但是设mac filter list。这样只有我认可的mac地址的网卡才能接入网络。本身网络也没有啥贵重的东西，这样很合适。
3.华硕路由器官方出来的时候，就已经是内置了下载大师了，可以搞定ED2K，BT等链接。不过对我们来说，用习惯了迅雷的离线下载，其他直接无爱。路由器内部必须要先设置好下载环境，才好开始用。我绕路了，浪费了不少时间，索性就将tip直接说出，大伙也节省些时间。

####路由器下载环境设置

首先，不要使用路由器界面tools下的run-cmd,曾经有一段时间我以为这就是终端操作的地方，我错了，在发现错误之前，时间已经过去2个小时，没错，有些小笨。第二，merlin说要用telnet登录，如果你不知道如何用telnet登录，那你也别折腾了。我确实不会telnet登录，但是你要是会ssh登录也是一样的。但是登录之前也要打开，在administration-system标签下开启。我原来竟是没有找到。ssh登录后，之后按照[这里的指示](https://github.com/RMerl/asuswrt-merlin/wiki/Initialize-OPTWARE)安装即可。我的理解是就是在连接的usb盘上创建一个linux的工作环境，可以安装必要的下载工具，比如aria2c或是wget，以及迅雷（当然，看完下一段的介绍后，其实只要安装aria2c就可以了）。


####全新的下载方式

使用[ThunderLixianExporter](http://blog.binux.me/ThunderLixianExporter/),将书签拖到地址栏中，进入自己的离线下载页面后，点击书签在出现取回本地的按钮中选择aria2.选择弹出框中的链接，一般情况下直接就可以进行下载了。我的情况比较特殊，需要在末尾加上--event-poll=select。

速度稳定在3M/s到5M/s之间，作为路由器附属的硬盘，我对这个速度相当满意了。以后，即时不在家里，远程登录上路由器，下个命令，到家打开电视就可以看了。确实很方便。

![](http://farm9.staticflickr.com/8353/8254406824_85593192e1_z.jpg)

如果这种方式可以证明相当稳定的话，我想以后可能就不需要用[fakeThunder](http://martianz.cn/fakethunder/)（非mac系统无法使用），或是linux下的[xunlei lixian](https://github.com/iambus/xunlei-lixian)（我发现在路由器中下载的速度波幅太大）。

最后值得一提的是，批量下载时通过aria2c的文件命令输一次就OK了，配合上screen命令，在终端窗口输出后，直接关上就完事，不要太方便。
