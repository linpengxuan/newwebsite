---
layout: post
title: "Octopress博客从零开始 II"
date: 2012-01-08 08:13
comments: true
author: linpx
categories: octopress computer
---
现在开始进行下一步**octopress的安装**。

我之前做过的octopress博客是这样更新的，vps上安装了，本地电脑也安装了，两个同时和github同步保持更新。同时，域名绑定vps的ip地址，用nginx来显现网页。因为我对git不熟悉，经常会出现一个问题就是更新出现问题。<!--more-->

octopress要求有rvm和git等配置好，我直接列出在**ubuntu**下直接安装的命令行，下次我考好直接贴入就可以了。
{% codeblock %}
apt-get install git git-core git-gui git-doc curl gcc build-essential
groupadd rvm
useradd -g rvm rvm
usermod -a -G rvm root
bash < <(curl -Lk https://github.com/wayneeseguin/rvm/raw/1.3.0/contrib/install-system-wide)
echo '[[ -s "/usr/local/rvm/scripts/rvm" ]] && . "/usr/local/rvm/scripts/rvm"' >> ~/.bash_profile
source ~/.bash_profile
rvm pkg install zlib
rvm pkg install openssl
rvm install 1.9.2 -C --with-openssl-dir=$HOME/.rvm/usr
rvm --default use 1.9.2
apt-get install libcurl4-openssl-dev libssl-dev zlib1g-dev libreadline5-dev
ruby -v
{% endcodeblock %}

这时我们需要在github网页上配置一个新的源，猛击[这里](https://github.com/repositories/new)开启新源。
{% codeblock VPS %}
cd ~;mkdir .ssh;cd .ssh #vps上制作ssh登录key
ssh-keygen -t rsa -C "your@email.com"
vi id_rsa.pub #考入github public key中
ssh -T git@github.com #测试是否可以登录,注意将原先已有的authokey2文件保存后再拷回来，保证本机登录vps的无密码要求功能
git config --global user.name "your name"
git config --global user.email your@email.com
{% endcodeblock %}

继续octopress的配置
{% codeblock %}
mkdir /home/wwwroot;cd /home/wwwroot
git clone git://github.com/imathis/octopress.git blog;cd blog #我的博客安装在/home/wwwroot文件夹下，如果直接安装到root下，nginx是无法正常显示的。
ln /home/wwwroot/blog ~/blog -s #在根目录建立一个快捷方式
gem install bundle
rake setup_github_pages #将刚才新设好的github page地址考入git@github.com:yourname/blog.git
git remote add origin git@github.com:yourname/blog.git
rake install
rake generate
rake deploy
git add .
git commit -m "blahblahblah"
git push origin master
{% endcodeblock %}

以上做完后你的博客就已经设置好了。关于git有些补充：

*	用`git remote －v`来看看push的设置，刚开始初始化完的时候，你只有一个无法远程push的git源，也就是octopuses作者的git源`git://github.com/imathis/octopress.git`，我们需要通过这条命令更新为自己的git源`git remote add origin git@github.com:linpengxuan/test.git`,这里origin只是一个名词，一个代号，很遗憾我花了n长时间才发现这个`origin`是可以改成自己想要的任何名字，比如`mown`，这样的结果是，我们在和网站同步的时候就变成了`git push myown master`;同样的，`master`也是本地机子的源的名字，所以命令的意思就是**push to myown from master**。类似的`git pull origin source`意思为**pull from origin to source**。

*	`git add .`的意义在于给每个文件创建个时间点，在这个时点给文件命名为后面的commit的内容`blahblah`。当我们修改了文件以后，另一个时点再commit一个不同的内容`blah II`。这样的意义就是你可以取回不同时点内容变动前后的相同文件，这也就是github很有意义的地方，版本控制。这也是我整了很久才明白过来的地方。

因为我需要把博客放在自己的网站上，我打算先安装nginx到vps上，我的网站内容。之前装过[LMNP一键安装包](http://lnmp.org/)，实在太吃我内存了，256mb的xen vps用`free`一看，就只有几兆的空余，所以最好的方法就是只装一个LMNP中的那个N，也就是nginx。
{% codeblock %}
echo "deb http://nginx.org/packages/ubuntu/ lucid nginx" >> /etc/apt/sources.list
echo "deb-src http://nginx.org/packages/ubuntu/ lucid nginx" >> /etc/apt/sources.list
apt-get update 
apt-get install nginx
service nginx start
{% endcodeblock %}
然后调整nginx内容，指向博客首页。
{% codeblock %}
vi /etc/nginx/conf.d/default.conf #在server框内插入以下内容
server_name  xxx.com; #网站的域名 
location / {root   /home/wwwroot/blog/public;
                   index  index.html;}
service nginx restart #回到目录下进行重启nginx服务
{% endcodeblock %}

回到目录下，再调整`_config.yml`。
{% codeblock %}
cd ~/blog
vi _config.yml #这里需要改以下两个地方,destination不改的话，你的网站就没有theme了
root: /
destination: public/
rake generate;rake deploy #要备份的话，可以commit到origin，一般网站更新到这步就可以了
{% endcodeblock %}
这时候打开你的vps ip地址，应该就可以看到octopress博客了。

####关于博客域名绑定
*	我先将域名绑定至网络免费的dns服务提供商，我用的是[freedns.ws](http://freedns.ws),在上面将域名指向vps ip地址。
*	因为域名是在webhost上买的，所以webhost有个可以更改nameserver的地方，将这个指向freedns服务商提供的nameserver就可以了。
*	然后等待1～24小时让这个更改内容在网络进行propogate。

到这儿，octopress博客已经完全搭建起来了。直接就可以用如下命令来进行博客的发布。呼，长嘘口气。
{% codeblock %}
rake new_post["filename"]
vi source/_posts/2012-01-07-filename.markdown #起名方式为日期，然后跟着post名称，如果是中文的话，会自动被整理成拼音,记得在开篇设置author: name以及category: 类别等
rake generate
rake deploy
{% endcodeblock %}

