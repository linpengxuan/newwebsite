---
title: '如何在越狱iPhone上安装虾米。'
date: 2012-04-25 21:22:33
author: linpx
categories: computer
comments: true
layout: post
markup: markdown
published: true
---
数次想在越狱的iPhone上安装xiami，均因为麻烦而放弃。总结完这次，希望可以一劳永逸。
1. 下载最新的[虾米软件](http://www.xiami.com/software/iphone)。
2. 在cydia上安装openssh和iFile，installous，破解版可从该源获取`http://apt.178.com`
3. 在苹果电脑终端上运行`scp xiamiiphone_x.x.x.ipa root@10.0.1.4:
/var/mobile/Documents/installous/Downloads/`。其中`10.0.1.4`为iPhone的IP地址。密码为：alpine
4. 打开iPhone中的installous中的downloads，点击安装即可。
