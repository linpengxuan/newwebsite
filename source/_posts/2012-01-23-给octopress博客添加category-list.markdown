---
title: '给octopress博客添加category list'
date: 2012-01-23 20:46:39
author: linpx
categories: octopress computer
comments: true
layout: post
markup: markdown
published: true
---
在前几篇博客我有提到过，我还不大会设置category
list。博客有这功能后，当然会更好浏览了。作为一项任务，必须攻克。于是大年初二，家里也没啥亲戚可逛，也没有太多电话短信去发，又上google搜去了，闲着也是闲着，挺好不是？

####设置
当然还是先得去GitHub上去找前人做好的程序了，这里采用了[nistude](
https://gist.github.com/nistude)写的[gist:1144723](https://gist.github.com/1144723)。
下面贴出的是我fork后修改的内容。
{% gist 1661725 %}

我解释一下用法。gist下载后放到octopress/plugins/下即可。然后建立`source/_includes/asides/categories.html`文件。内容如下：
{% codeblock %}
<section>
 <h1>Categories</h1>
 <ul id="categories">
 {% category_list %}
 </ul>
</section>
{% endcodeblock %}

然后我们在`_config.yml`中的`default_asides`条目中仿照同类，中括号内加入`asides/categories.html`就设置完了。这时`rake
generate`一下就出来了。
####需要注意的地方
*    gist文件17行设置的为category的绝对路径，可按需进行修改。
*
不完善的地方是，它无法辨识带有大写字母的category和或是中间有空格的单词串category。所以我昨天花了不少时间进入原来的博文，修改原有的category。既然我需要的基本功能可以使用，就不多折腾啦。
