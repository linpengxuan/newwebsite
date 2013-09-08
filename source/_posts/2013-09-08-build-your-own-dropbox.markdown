---
title: 'build your own dropbox'
date: 2013-09-08 16:01:26
author: linpx
categories: computer
comments: true
layout: post
markup: markdown
published: true
title: ownCloud, 建个自己的私人云
---
以此文献给林太太，周末没有相陪请原谅。
####1. 平台选择
工作的原因，我需要建立一个数据协同分享平台。最方便的就是dropbox，零设置，安装好就可以用。我并未选择该平台，原因有几个：一是我想建一个数据分享平台可以和我之前建立的[部门内部wiki](
http://www.sifd.us)形成连动，简而言之，就是放到文件夹内，就自动生成wiki_link，方便书写条目；二是领导有要求，数据要保密。

很明显，我把目光移到了开源的项目上，选择其实很多，比如：[迷你云](http://www.miniyun.cn/)，[ownCloud](
http://owncloud.org)；还会有很多[其他选择](
http://creativeoverflow.net/the-10-best-alternatives-to-dropbox/)
，这里就不一一累赘。我选择了ownCloud，号称是
>This is the alternative most of all geeks have. As the name suggests with
this web app you can create your own cloud home.

<!--more-->

####2. 安装
因为已经有了[安装wiki的经验](http://colors4.us/blog/2013/08/01/dokuwiki-diy/)
，此类运用到php的程序是很轻松就可以安装的。具体可以参考以下几个链接：
1. [官方文档](
http://software.opensuse.org/download/package?project=isv:ownCloud:community&package=owncloud)，for
ubuntu VPS
2. [manual installation](
http://doc.owncloud.org/server/5.0/admin_manual/installation/installation_source.html
)
######2.1. 关于权限
细化下来就是很简单了。utunbut的程序安装好后，会在`/var/www`上建立好`owncloud`文件夹。**注意**，当刚下载好后，会因为权限问题而无法访问。原因很简单，文件访问时，是通过系统的php程序调取内容的，访问权限最大也仅以php访问权限为限，所以如果考下的文件仅有root打开权限，网站打开一定会报错的。需要咋么做呢，很简单。首先，在owncloud目录下建立test.php，具体内容如下图所示。我假设你已经成功地将网址`
http://www.a.com`指向了该目录，在浏览器敲入`http://www.a.com/test.php`，将会得到如`Your PHP
process seems to run with the UID 33 (www) and the GID 33
(www)`的一句话，意思就是php的组分到了`www:www`, 权限打开就很明白了，在远程VPS端运行`chown -R www:www
/var/www/owncloud`。ubuntu配apache2亲测可用，nginx有时行有时不行，我晕。

```
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
```
######2.2. 关于编码
如果VPS服务商提供的系统很精简，往往会省略很多多语言支持。这样很明显的例子就是开始使用时，原本是汉字的文件，变成了乱码。敲入` locale-gen
en_US.UTF-8`命令即可解决。

####3. ownCloud进阶
ownCloud以开源的基础，开放了插件开发系统，程序猿同志们很热心地为其开发了很多插件，我挑一个对我有用地给做个介绍。
######3.1. 和dokuwiki连动
嗯，说实话，本文实在太小众了，基本不会有这样的情况吧。首先，要有建立wiki的需要，大约30%的人会选择dokuwiki；其次，会不愿用dropbox而转至建立私人的云端服务器，并选择ownCloud，这个我认为是目前市面上最NB的开源云端。第三，会想到，是不是有两者连动的可能性，并愿意花上3,4天时间折腾之间的交互，因为，相信我，你读到的博客内容是远比我自己摸索的内容简单太多了。
我上文已经叙述曾经建了一个[dokuwiki](http://www.dokuwiki.org
)，该平台最大的好处就是备份相当容易，只需将文件打个包就可以了，没有任何其他滑头，因此很受我的幻影。但是有个小问题，它的文件上传管理系统很坑爹。我们部门内部每隔一段时间就要传些文件上去，得一个个走，拷贝下载链接，自己书写wiki下载条目，麻烦得很。用ownCloud的dokuwiki链接就可以一站解决所有的问题。在继续下去之前，我假设各位的的两个网站文件夹都放在了`/var/www`下，分别为`wiki`和`owncloud`。
正如前述，通过ownCloud的插件，我们可以将文件直接放到电脑客户端对应的文件夹内，之后系统会自动上传到dokuwiki的文件管理系统中，并生产wiki_link，我们需要做的就是在编辑条目时，考入该link，做到零麻烦。那么就开始我们的设置之旅吧。
我参考了网址：[ownCloud Plugin installation](
https://www.dokuwiki.org/plugin:owncloud)。
######3.2. ownCloud端插件安装
按如下方式来做。
```
cd /var/www/owncloud/apps
wget https://codeload.github.com/lebowski42/owncloudapp-dokuwiki/zip/master
unzip master #将解压后文件夹改名为dokuwiki
chown -R www:www dokuwi  #根据php的组和用户名对dokuwiki文件夹赋权，否则无法显示
```
接下来，我们进入网页端右上角setting的`apps`。因为该插件是在apps
`versions`改造完成的，所以在开启之前需要disable该插件，并打开`external storage
support`。之后，参考前文网址一步步设置下来就好。
设置好后，在你的文件夹中会出现一个`wiki`文件夹，通过`external storage
support`挂载了本地地址`/var/www/wiki/data/media`。点击进去后，就可以看见wiki的选项，点击后就生成如同`{{
:previous_files:getqrcode.jpg?nolink&200&fileid=133
|下载}}`的wiki_link，用起来很是方便。
######3.3. dokuwiki端插件安装
wiki网页端不需要安装任何插件，如果安装安装手册来说，插件都给安上了，反倒有时候无法更新条目。出现此问题时，将插件删除即可。
另外，需要注意的是ownCloud的`wiki`文件夹的文件命名规则是走dokuwiki的。
  - 只能用小写英文和数字，不能出现汉字，大写字母；
  - 不能有空格，用`_`代替。
######3.4. 为何internal error 500
问题马上就来了。ownCloud在一打开dokuwiki插件后，就停止工作了。这真是个令人烦恼的地方。如果在google上搜这些关键词，能找到各种各样的说辞和解决方法，就是没有一项和这个毛病沾边。问题是，我的这两个系统联动太小众，说句难听的话，我很有可能是这个星球上第一次出现这个问题的人，即使插件作者也没有。我把问题可能出现的问题都想想，可能出在apache2的virtual
host上，可能出在`.htaccess`上，可能是权限有问题，基本上，任何可以出问题的地方，都可以是问题所在。我折腾了一天，都要放弃了。在某个网页提示我，要不看看`/var/log/apache2/error.log`,
出了错总归会有记载的。就抱着试试看的心态就打开了。然后毅然发现错误所在。error.log上说我的某个php程序软件第10行代码有问题，`vi
/var/www/owncloud/apps/dokuwiki/lib/utils.php`后，发现问题所在，作者默认的wiki的文件夹名字是`dokuwiki`，而我自己为美观，起了一个`wiki`文件夹名称。于是，就这个小小的问题，花了我3天时间。谁能知道呢。
######3.5. direct link下载
如果你自己有了服务器了，还无法生成public link分享给别人，是不是觉得很不爽。那我现在告诉各位，这个问题不存在啦。
```
mkdir /var/www/owncloud/4dl
chown -R www:www /var/www/owncloud/4dl #根据php所属组别填.做了个测试，连这部赋权都不需要了
```
完事后，把任何想要做直链的文件移入就好。微信共享更简单，我这里分享个ownCloud的安卓app，一扫就明白我的意思了。

![](http://cloud.sifd.us/4dl/android_app_qrcode.png)

####4. 小结
周末的时间能把整个协作分享平台做好，我觉得很有成就感。尤其是其中能够发现错误并改正过来，让我觉得自己太牛了。学到很多东西，果然干中学是最好的老师。我希望能够把这股热情也同样放到研究上面。一句话，加油吧。
