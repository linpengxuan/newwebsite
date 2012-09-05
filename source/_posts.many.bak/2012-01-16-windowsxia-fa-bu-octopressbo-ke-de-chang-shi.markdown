---
layout: post
title: "windows下发布octopress博客的尝试"
date: 2012-01-16 03:00
comments: true
author: linpx
categories: octopress computer
---
#####多此一举
以下经证明，属多此一举。在任何需要输入中文的情况下，只需在word下写好，拷贝入terminal cocoons即可，即使表现出来是乱码也勿挂，谨记各步骤即可。
<!--more-->
本篇文章适用于将octopress发布系统配置在服务器上的朋友。

有的时候需要发布博客，手头上只有windows电脑，还是借别人用的，异常绝望。因为我们通过windows以ssh方式登录上服务器后，发现输入中文时，出来全是乱码。rake generate不认怎么办。我想了个绕过去的方法，不用任何设置，只需要有`Dropbox`即可。

先解释我的方法。先用markdown写好博客，notepad下以UTF-8格式存好，放入Dropbox的`Public`文件夹，这样我们会得到一个链接，拷贝链接；在服务器端下，先设立新文档，命名新文档(建议按英文名命名，中文不识别的缘故),然后wget 拷好的链接，用cat命令将取回的博客添加到新文档后面，以保存完整的ymal的metadata。看看如下的代码就明白啦。

{% codeblock 服务器端 %}
cd octopress
wget http://dl.dropbox.com/u/389XXXX/1.txt   #your markdown file address
rake new_post[“your new blog English name”]
cat 1.txt >> source/_posts/2012-01-16-your-new-blog-english-name   #your file to edit
rake generate
{% endcodeblock %}

