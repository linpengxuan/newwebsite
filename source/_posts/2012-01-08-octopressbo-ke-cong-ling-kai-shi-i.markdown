---
layout: post
title: "Octopress博客从零开始 I"
date: 2012-01-08 07:45
comments: true
author: linpx
categories: vps octopress computer
---
本文的写作原则是以我这个没有学过程序的文科同学为基础，所以会有很多其他哥们看了觉得没事，可在我这就得想破脑袋才能整出来的(呵呵，擦汗)。写这篇文章的两个原因是，向别人分享我安装这个的经验，减少无用功；以及可以让我以后在更换服务器的时候，不用浪费太多时间在我曾经做成过的东西。这次的策略是，我只打算用vps上的octopress来更新博客，简单些，应该好些。放在vps上更新的好处是，换了电脑也能更新博客，只要有ssh软件的话。<!--more-->

到[这儿](http://www.webhostingtalk.com/forumdisplay.php?f=104)来挑个你心仪的VPS服务吧。我选择了[inceptionhosting](http://inceptionhosting.com/),4欧元可以拿到美国的Xen VPS，256MB，以及直接就可以安装设置好openvpn的distro。我认为这是个相当不错的选择。

购买之后，进入VPS control panel，选择安装设置好openvpn的ubuntu 32bit就可以了。（这个distro只是个人爱好。）

然后就是SSH进入已经设置好的服务器`ssh root@x.x.x.x`。这里，我可能会想用免密码登录ssh，那么可以参考这篇[文章](http://www.chinaunix.net/jh/4/548851.html);{% codeblock 本地 %}
ssh-keygen -t rsa
ssh root@x.x.x.x "mkdir .ssh;chmod 0700 .ssh"
scp ~/.ssh/id_rsa.pub root@x.x.x.x:.ssh/id_rsa.pub
{% endcodeblock %}
{% codeblock VPS %}
touch /root/.ssh/authorized_keys2;cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys2
{% endcodeblock %}进去后就先设置一下合用的openvpn帐户。这里我用
{% codeblock %}
passwd openvpn
{% endcodeblock %}
先设置好密码，然后就可以登录`http://x.x.x.x:943`根据提示下载`client.ovpn`进行openvpn连接。是不是有种史上最快vpn设置的感觉。lol
然后请倒上一杯咖啡移步[Hulu](http://hulu.com)或是[Spotify](http://www.spotify.com)(下文没有提示，所有操作都默认在VPS上进行)。

设置好一些基本语言环境内容。
{% codeblock %}
echo "export LC_ALL=en_US.UTF-8" >> /etc/environment
echo "export LANG=en_US.UTF-8" >> /etc/environment #设置后需要reboot
locale #可以用locale查看一下环境变量
{% endcodeblock %}

上了VPS要是不走个VPN，我这老脸还真不好意思。因为在已经又openvpn上的系统上整vpn相当容易。我这里参考了这篇[文章](http://blog.cuoluo.net/2009/12/install-pptp-vpn-in-linode-vps/)（呵呵，主要这是google搜索跳出的文章）。
{% codeblock %}
apt-get update
apt-get install pptpd vim #iptables也是要装的，我的distro已经包括所以省略了
echo "localip 192.168.0.1" >> /etc/pptpd.conf; echo "remoteip 192.168.0.234-238,192.168.0.245" >> /etc/pptpd.conf
echo "username pptpd passwd *" >> /etc/ppp/chap-secrets 
echo "ms-dns 8.8.8.8" >> /etc/ppp/options;echo "ms-dns 8.8.4.4" >> /etc/ppp/options 
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf 
sysctl -p
/sbin/iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
/etc/init.d/pptpd restart
{% endcodeblock %}
上面这段代码用在ubuntu 11或10上都能够直接连接上vpn，用户名和密码请相应做出修改。它不能做得是，在vps重启动以后，需要单独再运行一下这条命令。
{% codeblock %}
/sbin/iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
{% endcodeblock %}
呵呵，我比较不太懂怎么自启动运行，回头整好了，再统一更新。我个人是用这个土方法来解决。
{% codeblock %}
echo "/sbin/iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE" >> ipt.sh
chmod 755 ipt.sh
./ipt.sh #每次启动运行一次吧，希望不用太多重启。
{% endcodeblock %}

在使用vim时，我感觉对光标控制和输入很无力，参考[vim的使用方法](http://www.chinavim.org/vim-%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95%E7%AE%80%E4%BB%8B.html)后，才觉得vim真好用，真是好用啊。

到目前为止，在vps上实现了如下几个用途：

1. 免密码登录ssh
1. 设置openvpn
1. 设置vpn
