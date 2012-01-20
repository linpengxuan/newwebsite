---
title: '利用邮件发表octopress博客'
date: 2012-01-19 19:25:05
author: linpx
categories: octopress computer
comments: true
layout: post
markup: markdown
published: true
---
看完这篇文章，我想大伙应该就可以实现通过email直接在octopress博客上进行发文。好处相当明显，你不可能永远带着你配置有octopress的电脑，只要手里有手机发邮件，就没有问题。真正做到**anytime
anywhere**.

写文章的初衷是，本[网站](http://colors4.us)是[我](
http://twitter.com/linpx)和我[太太](http://twitter.com/sourrabbit)
共同经营的博客。很多时候，她写好了文章，委托我发表时，苦于手中的工作windows电脑，发布相当不易。就开始设想有没有这么个功能，用email发布博客。答案是有。在和[@lucifr](
http://twitter.com/lucifr)交流后，发现[@masukomi](http://twitter.com/masukomi)的方法很靠谱，可以使用，于是在不懂任何环境配置的情况下，瞎猫碰到死老鼠地运气和从`windows
95`就开始养成的反复安装，反复测试的精神的鼓励下，开始了摸索。

这篇文章适用于在自己域名上布置的octopuses博客，只需`rake
generate`就能在nginx上完全反映出内容的变化。如果是用github的朋友，还需多加上`rake deploy`和相应的`git
commit`。实际上从我之前所写的`Octopress博客从零开始`出发，到这篇文章为止，是可以做到零脑损建立octopress博客的。<!--more-->

和原来发布方式有个不同的地方是，原来的博文地址都是中文拼音，这次更给力，地址用上中文了。另外，在windows远程登录ssh设置时看到的都是乱码，实际上不影响的，换台mac，就没有问题。

####jekymail配置

{% codeblock %}
git clone git://github.com/masukomi/JekyllMail.git /home/wwwroot/jekyllmail
cd /home/wwwroot/jekyllmail
rvm --default use 1.9.2
apt-get install libxslt-dev libxml2-dev
gem install nokogiri
bundle install
{% endcodeblock %}

进入jekyllmail文件夹，修改`_config.yml`,具体内容如下:

{% codeblock %}
blogs:
- jekyll_repo: /home/wwwroot/blog     #这是我服务器上布置的octopress博客文件夹
  source_dir: /home/wwwroot/blog/source   #这个文件夹下包括_posts, _includes等
  pop_server: pop.gmail.com
  pop_user: xxx  #自定义的邮件接收地址(也就是xxx@gmail.com)
  pop_password: yyy  #自定义的邮件登录密码
  secret: 123!  #secret作用为让jekyllmail区别邮件是否为你想要发送的博文，自己设个吧
  markup: markdown
  site_url: http://colors4.us  #这里是我的网址
  commit_after_save: true
{% endcodeblock %}

邮件发送博文牵涉到两个邮件，发送的邮件和接收的邮件，jekyllmail会自己登录到接守的邮件上去检查是否有需要发送的博客。所以，我建议两个都用
gmail，确认设置中已经打开了pop3支持和utf-8发送。

是不是超级简单。我们现在可以测试一下是否有用。进入自己的gmail信箱给`xxx@gmail.com`发新邮件，邮件题目subject
如下，更详细的情况可参考程序作者[@masukomi](http://twitter.com/masukomi)的[git页面](
https://github.com/masukomi/JekyllMail):

{% codeblock %}
这里是博文的题目中文亦可 || secret: 123! / author: linpx / categories: test ok /
comments: true      #categories这里很特殊，各种目直接用空格隔开就行了，不要添逗号
{% endcodeblock %}

邮件的内容直接拷入已经写好的markdown格式的文章即可。我建议书写格式去除`rich formatting`，改用`plain text` (这是
gmail内置的功能)。然后点击发送。

{% codeblock jekyllmail folder %}
ruby jekyllmail.rb
{% endcodeblock %}

如果你能够在`/home/wwwroot/blog/source/_posts`中看到你刚才发过去的邮件，那么你的jekymail配置就是成功了。(这
里我首次测试是不成功的，百思不得其解，后来发现发的测试邮件在第一时间被我另一个设好crontab的服务器给接走鸟。-_-!!!)
####ubuntu下cron设置
很明显，要让博文自动发送，必须有自动机制，程序作者程序作者[@masukomi](http://twitter.com/masukomi)
也建议用cron。cron是linux下的定时器，定时运行程序。我在这里的设置相当乱，因为我对cron不熟悉，能用的原因是我一个一个试出来的，笨方法，大家别学。

* cron在运行的时候，是不会采用我们已经有的环境的。所以很多情况下，我们直接运行ruby
xxx.rb可以，但是在cron下是不行的，因为没有定义rubi环境。这个很重要，我花了3个小时才捉摸出来。
*    ubuntu下的cron设置很容易，在任何目录下运行`crontab -e`就行，存盘出来后应该就是已经放入到系统运行中去啦。
*    cron的语法必须要了解，大家可以看看这个[页面](https://help.ubuntu.com/community/CronHowto)。

我直接给出我可以用的设置，修改文件夹位置后，引用即可。输入`crontab
-e`,加到最后一行。内容中涉及到`gem`和`rvw`的地址，用`which gem`和`which rvm`来确定。
{% codeblock %}
* * * * * /bin/echo "check if cron works" >> /home/wwwroot/1.log
#此命令为每隔一分钟写入log文件，目的用于测试cron是否正常工作，确认后可直接删除。
* * * * * cd /home/wwwroot/jekyllmail && ./run_jekyllmail.sh
#每隔5分钟登录检查邮箱，是否有博文，强迫症轻度患者建议改为每一分钟。
* */2 * * * cd /home/wwwroot/jekyllmail && ./build_site.sh   #每隔2小时做一次rake
generate
{% endcodeblock %}
cron运行时时不带环境的，所以我们必须在`jekyllmail`下的两个文件定义环境。设定如下：
{% codeblock run_jekyllmail.sh %}
#!/bin/sh

[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load
RVM into a shell session *as a function*
export LANG=en_US.UTF-8
export GEM_HOME=/usr/local/rvm/gems/ruby-1.9.2-p290
export
GEM_PATH=/usr/local/rvm/gems/ruby-1.9.2-p290:/usr/local/rvm/gems/ruby-1.9.2-p290@global
export
PATH=/usr/local/rvm/gems/ruby-1.9.2-p290/bin:/usr/local/rvm/gems/ruby-1.9.2-p290@global/bin:/usr/local/rvm/rubies/ruby-1.9.2-p290/bin:/usr/local/rvm/bin:$PATH
#各机子环境有所不同，确认后代入

bundle exec ruby jekyllmail.rb
{% endcodeblock %}
以及{% codeblock build_site.sh %}
#!/bin/sh

[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load
RVM into a shell session *as a function*
export LANG=en_US.UTF-8
export GEM_HOME=/usr/local/rvm/gems/ruby-1.9.2-p290
export
GEM_PATH=/usr/local/rvm/gems/ruby-1.9.2-p290:/usr/local/rvm/gems/ruby-1.9.2-p290@global
export
PATH=/usr/local/rvm/gems/ruby-1.9.2-p290/bin:/usr/local/rvm/gems/ruby-1.9.2-p290@global
/bin:/usr/local/rvm/rubies/ruby-1.9.2-p290/bin:/usr/local/rvm/bin:$PATH

cd /home/wwwroot/blog/ ＃你的octopuses文件夹目录
bundle exec rake generate
{% endcodeblock %}

通过cron定时进行`rake generate`必须补充一下，如果你的网页是放在github上，还需来个`rake deploy`。
####暂时结语
上述方法不是最优的，其实我希望可以做到[@masukomi](http://twitter.com/masukomi)所写的[ServingOctopress
From a Self-hosted Git Repository](
http://weblog.masukomi.org/2011/12/19/serving-octopress-from-a-self-hosted-git-repository/)
（中文版可参考[@lucifr](http://twitter.com/lucifr)所写博客[服务器端 Octopress 搭建及移动方案](
http://lucifr.com/2011/12/21/octopress-on-server-and-portable-scheme/)),文中的新博客文章放到特定文件夹后会主动触发`rake
generate`，在我看来，这远比用cron被动，反复rake来的即时和漂亮。我在做测试时，失败了，我会在以后继续跟进这个方向，有新的进展，肯定贴出。

同时欢迎大伙在下面评论中留下各种问题，只要我知道的，会非常乐意交流。
