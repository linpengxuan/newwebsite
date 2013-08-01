---
title: 'dokuwiki DIY'
date: 2013-08-01 11:12:10
author: linpx
categories: computer
comments: true
layout: post
markup: markdown
published: true
title: 来建个wiki吧
---
因为工作需要，我们需要在部门内部设立一个同事互相交流的园地。可以让每人都可以有修改权限，可以上传文件，可以留言评论。很自然，我想到了维基百科。下面我将分享如何在VPS上从头开始设置一个全新的百科，即使IP上已存另一域名网站。
#####wiki选择
因为一开始就很明确，只要个最简单的wiki，不需要数据库管理，同时备份也异常方便的。我建议从[wikimatrix](
http://www.wikimatrix.org/wizard.php)这个网页上去挑选合适自己的wiki。网站最后建议我选择[dokuwiki](
www.dokuwiki.org)。
####wiki安装
我曾经以为wiki的安装很繁杂，我错了。安装很轻松，有如wordpress的安装一般，只需要注意几点就行。<!--more-->

dokuwiki需要php的环境，对于新手来说，我建议直接安装[LNMP一键安装包](http://lnmp.org/install.html)
，点击根据提示安装即可。注意编译时间较长，要有耐心。安装好后，php环境和之后需要的网页显示的nginx就一步到位搞定了。

{% codeblock %}
cd /home/wwwroot; wget
http://download.dokuwiki.org/out/dokuwiki-a1b9b25f129b085a00920bd821719ccd.tgz;
tar -xvf dokuwiki* . #解压wiki到/home/wwwroot/目录即可
{% endcodeblock %}

解压后就很方便了，直接设定好nginx指针后就可以直接进行设置。

{% codeblock %}
cd ~;
./vhost.sh #如果LNMP一键安装包安装完毕
{% endcodeblock %}

假设VPS的IP地址为1.2.3.4, 已经有了一个网站www.a.com; 这时我们需要在该地址上再设立一个www.wiki.com
的网址，用vhost.sh设置就很容易了。运行`vhost.sh`后，根据提示再输入第二个网站的的地址和网页index所在地址，这里为`/home/wwwroot/dokuwiki`，很轻松就做到了一个IP地址，两个不同网页地址。当然，大前提就是新的网址在购买服务商那儿DNS设置指向上到了VPS的IP。

做完之后，就是在电脑浏览器上输入设定的网址`www.wiki.com`，在没有任何设置的情况下，网页会提示进行wiki初始化设置。

很有可能在此情况下，wiki安装包文件的读写权限会出现问题。我曾在这里耗费了很长的时间，这里我直接给出解决方法。主要参考[该网页](
https://www.dokuwiki.org/install:permissions).
我只给出结果，不给出原因，因为我也不知道，都是试出来的。首先在dokuwiki文件夹内建立一个新文件`vi
/home/wwwroot/dokuwiki/phpinfo.php`,文件内容如下：

{% codeblock %}
<?php

if(function_exists('posix_geteuid')){
    // use posix to get current uid and gid
    $uid   = posix_geteuid();
    $usr   = posix_getpwuid($uid);
    $user  = $usr['name'];
    $gid   = posix_getegid();
    $grp   = posix_getgrgid($gid);
    $group = $grp['name'];
}else{
    // try to create a file and read it's ids
    $tmp = tempnam ('/tmp', 'check');
    $uid = fileowner($tmp);
    $gid = filegroup($tmp);

    // try to run ls on it
    $out = `ls -l $tmp`;
    $lst = explode(' ',$out);
    $user  = $lst[2];
    $group = $lst[3];
    unlink($tmp);
}

echo "Your PHP process seems to run with the UID $uid ($user) and the GID
$gid ($group)\n"; ?>
{% endcodeblock %}

之后直接输入网址`www.wiki.com/phpinfo.php`,
应该可以获得本机目前的用户名和组名。假设我们获得的用户名为www-data，组名为foo。打入如下命令可以解决wiki的权限问题。
{% codeblock %}
    yourwiki> chmod -R 775 data/
    yourwiki> chown -R www-data:foo data/
{% endcodeblock %}

然后应该可以没有任何阻碍的完成`http://www.wiki.com/install.php`的设置。

####wiki使用

一开始我也是一头雾水。我提几个小点抛砖引玉，大家可以共同探讨。


  1. wiki需要一个sidebar，如何设置呢？很方便，进入该网页即可 `
http://www.wiki.com/doku.php?id=sidebar`.里面的所有设置都会出现在边栏导航上。
  2. 需要创建一个新页面叫aaa的，直接在地址栏输入 `www.wiki.com/doku.php?id=aaa`即可。
  3. 右上方有最近更改的页面，可以看到最新的更新。
  4.
dokuwiki的权限管理很强大。可以在管理员页面中设置。我自己的wiki的权限设置到这样的程度：用户注册之后获得初始组user是无法浏览的，必须由管理员手动更改至组member才有浏览修改权限，具有很强的私密性。以上组都是自己起名的，都可以改。

####综述

在安装dokuwiki过程，能学到不少东西。建立一个互相交流的平台，一定可以产生更大的价值。网站搭好后，需要通知同事加进来添加内容，这样才会让事情做得有意义。
