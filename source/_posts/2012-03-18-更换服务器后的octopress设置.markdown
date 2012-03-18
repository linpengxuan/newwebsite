---
title: '更换服务器后的Octopress设置'
date: 2012-03-18 17:39:03
author: linpx
categories: computer octopress
comments: true
layout: post
markup: markdown
published: true
---
每隔一段时间，我总会有些东西跳出来。事情是这样的，公司里面有道防火墙，屏蔽了Gmail。If there are something to be
got rid of my computer, I am quite positive the services from google would
the last one on my list. 于是我就用了翻墙软件来上Gmail，很讽刺，是吗？在肉身翻墙之后。。。

之前购买的VPS服务也许是因为我设置的邮件发布octopress的ruby程序，一个月内服务过载警告2次。于是我开始考虑转换一个[服务器](
http://newwebsite.com)。

#####概要，目的
1. 我想实现和现在VPS一样的功能；
1. 改善邮件发送功能。以前是使用cron定时器，每隔一定时间的运行，被动检查是否有新博客。我希望改善博客的发布功能；
1. VPN，SSH功能正常。

####服务器设置
我从[这儿](http://blog.s135.com/pptp_vpn/)可以设置好在CentOS上的VPN。

然后设置LNMP，因为我有了512MB的内存。

设置好新服务器的rvm环境和ruby。
{% codeblock %}
#安装git
yum install zlib-devel openssl-devel perl cpio
expat-devel gettext-devel autoconf
wget
http://www.codemonkey.org.uk/projects/git-snapshots/git/git-latest.tar.gz
tar xzvf git-latest.tar.gz
cd git-{date}   #进入git的文件夹
autoconf
./configure --with-curl=/usr/local
make & make install
#安装RVM
yum install git git-core git-gui git-doc curl gcc build-essential
groupadd rvm
useradd -g rvm rvm
usermod -a -G rvm root
bash < <(curl -Lk
https://github.com/wayneeseguin/rvm/raw/1.3.0/contrib/install-system-wide)
echo '[[ -s "/usr/local/rvm/scripts/rvm" ]] && .
"/usr/local/rvm/scripts/rvm"' >> ~/.bash_profile
source ~/.bash_profile
rvm pkg install zlib openssl libcurl4-openssl-dev libssl-dev zlib1g-dev
libreadline5-dev
rvm install 1.9.2 -C --with-openssl-dir=$HOME/.rvm/usr
rvm --default use 1.9.2
gem install bundle
#继续octopress的配置,从github上恢复
cd /home/wwwroot
mkdir blog
cd blog
git init
git remote add origin.old git://github.com/linpengxuan/blog.git
git pull origin.old master
git remote add origin git://github.com/linpengxuan/xinboke.git
git add .;git commit -m "aa";git push origin master
#设置VPS端的另一个octopress环境，用于自动rake generate
cd /home/wwwroot
mkdir octopress_blog.git && cd octopress_blog.git
git init --bare
git config core.bare false
git config core.worktree /home/wwwroot/blog
git config receive.denycurrentbranch ignore
vi hooks/post-receive
/bin/sh
GIT_WORK_TREE=/home/wwwroot/blog git checkout -f
cd /home/wwwroot/blog
bundle exec rake generate
chmod 755 hooks/post-receive
cd /home/wwwroot/blog
git remote add live /home/wwwroot/octopress_blog.git

#设置jekyllmail
cd /home/wwwroot
mkdir jekyllmail;cd jekyllmail
git init
git remote add origin.old git:github.com/linpengxuan/JekyllMail.git
#设置脚本文件,设置根目录夹上aa.sh,内容如下
/bin/sh
cd /home/wwwroot/jekyllmail
export LC_CTYPE=en_US.UTF-8
export LANG=en_US.UTF-8
ruby jekyllmail.rb
cd /home/wwwroot/blog
git add .
git commit -m "aa"
git push live master

{% endcodeblock %}

####实际操作
每当我需要更新博文的时候，用邮件发送博文后，我会登录服务器运行`./aa.sh`,然后在有博文时，会自动`rake
generate`；在没有博文时，就保持原样。
