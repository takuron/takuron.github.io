---
title: aide安装m2repository库教程
description: 为aide安装Android Support Repository
slug: id0001
date: 2017-10-11 00:00:00+0000
categories:
  - Android开发
tags:
  - Android开发
  - Android
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

android support一向是Google官方提供给开发者解决应用兼容性和提供官方工具的好东西，这些都打包存在m2repository中。aide支持这个库，不过由于万里长城的影响，aide内置下载是有一定问题的，这里讲讲如何不用梯子下载安装最新版m2repository。

首先下载打包版本的m2repository，下载地址（推荐Google官方）：

[Google官方（国内已经可用）](https://dl-ssl.google.com/android/repository/android_m2repository_r47.zip)

[腾讯的国内镜像](http://android-mirror.bugly.qq.com:8080/android/repository/android_m2repository_r47.zip)

[外国镜像（非常非常慢）](http://mirrors.neusoft.edu.cn/android/repository/android_m2repository_r47.zip)

[百度网盘，提取码0202](https://pan.baidu.com/s/1qXD16RM)

下载完成后解压。注意：解压的时候最好选择“解压到当前目录”或“解压到指定目录”，如果没有这种选项下一步请保证选择的文件夹名字为“m2repository”。

以下以es文件浏览器解压为例，请选择2、3项而不是第一项。

![59771cf2e708b.png](https://i.loli.net/2017/07/25/59771cf2e708b.png)

解压后在aide中找到解压得到的文件夹，复制路径。

再次提醒：一定要保证复制路径的是文件夹名字为“m2repository”的文件夹，如果不是，请往里面寻找。

最后，在菜单里找到更多〉设置〉构建/运行〉maven仓库目录，把刚刚复制的路径粘贴进去即可。

![59771e30283c5.png](https://ooo.0o0.ooo/2017/07/25/59771e30283c5.png)

![59771e3d3cff5.png](https://i.loli.net/2017/07/25/59771e3d3cff5.png)

再在项目中引用类似android support v4之类的库，可以看到项目已经正常了。
