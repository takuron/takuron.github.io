---
title: 利用gitlab搭建静态页面并开启https
description: 基于无数踩坑经验，手把手教你搭建GitLab Pages。告别GitHub/Coding痛点，从静态网站到绑定域名、开启HTTPS，一篇搞定！
slug: id0008
date: 2018-02-25 00:00:00+0000
categories:
  - Web
tags:
  - Web
  - Gitlab Pages
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

踩了无数的坑终于搞明白gitlab pages如何搭建了（一般我写的东西都是自己踩坑后分享的，不喜欢写能找到的资料），决定写一写。

为什么不用GitHub或者coding？因为GitHub最近拦截了百度蜘蛛，懂SEO都知道这是对网站被搜索引擎收录的毁灭性打击，并且GitHub不支持自定义域名https，而coding需要人工审核还巨慢。所以这里我决定使用gitlab

与GitHub pages不同，gitlab pages的构建基于gitlab cl，也就是大部分pages的配置都基于 **配置文件而不是项目设置** ，连基本的纯静态HTML都需要配置文件，我们就从最基本的开始探究。

## 静态HTML的搭建

首先我们访问gitlab，点开[用户：pages的首页](https://gitlab.com/pages)，这里面是所有gitlab支持的静态pages类型，我们选择plain-html并克隆。

![005-KRocyly1foskji8qqoj31wq13egwl.jpg](https://i.loli.net/2020/02/07/hFr2vjPtECwmJUf.jpg)

（或者对于纯HTML这种简单的项目，你也可以自己新建.gitlab-ci.yml文件，里面会提示有模板，按照模板写好文件后新建public文件夹往里面放网页就可以了）

接着项目会自动进行一段时间的构建，构建好后就可以在Pages里看到网址了。

（如果想新建 用户名.gitlab.io 为网址的个人页与GitHub同理，把项目名命名成这个就好了）

## jekyll项目的搭建

对于jekyll项目，正常可以与静态HTML同理，最简单的方法当然是克隆示例然后制作，可是更多时候遇到的是准备部署模板，对于模板更好用的当然是自己创建配置文件时要注意 **一定要同时创建一个Gemfile文件** ，如果不知道怎么写可以直接复制官方的示例。（别问我为什么我也不知道，我只知道没有这玩意会失败）

## 绑定域名并开启https

如果你没有域名，那么对你而言本文已经结束了，因为gitlab的域名都自带https，这里要讲讲怎么自己申请自己域名的可信签名。

不同于国内的coding，gitlab要求你自己申请签名，这里我推荐[freessl.org](https://freessl.org)，首先打开网站，填入你的域名，公证机构选默认的就行。然后他会让你填邮箱和证书类型，其中要注意 **证书类型一定要选RSA** 其他的保留默认就好。

![005-KRocyly1fosl3r37lkj31wq12eagk.jpg](https://i.loli.net/2020/02/07/HXBquwj9nkSGxJZ.jpg)

然后他会进行签名认证，添加解析什么的不会自行百度。成功后就会产生两串字符串和一个下载按钮，建议把签名下载下来方便续签（其实注册账户也可以达到同样目的），还有签名是很重要的网站安全凭证，无事情不要随意透露private key。

接着我们到gitlab项目页，找到设置里的page，选择new domain会出现如下页面。其中第一个是域名，第二个和第三个是签名，把刚刚申请网页里的第一个复制到第二个框，第二个到第三个框就好了。

![005-KRocyly1foslys2t4mj31wq12eahm.jpg](https://i.loli.net/2020/02/07/LBWREYUswcyQCxg.jpg)

接着他会让你新建一个验证解析，这个是今年新加的来解决页面安全问题，老老实实添加解析我不解释了。（貌似后面可以删）

最后给域名添加 你的用户名.gitlab.io 的cname，再打开你的网站，完美。
