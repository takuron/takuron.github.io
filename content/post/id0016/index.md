---
title: OBS Studio在笔记本上黑屏问题的解决方案
description: 笔记本OBS黑屏？Optimus惹的祸！本文详解如何通过显卡驱动、Win10图形设置等方法，强制OBS使用核显，帮你彻底解决黑屏问题，告别录屏/直播困扰。
slug: id0016
date: 2020-03-16 00:00:00+0000
image: https://i.loli.net/2020/03/16/yH2f5qGeKJML3Wc.jpg
categories:
  - 软件使用
tags:
  - 软件使用
  - OBS Studio
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

众所周知，笔记本为了兼顾续航采用与传统台式PC不同的显卡使用模式，其中Optimus的确是某种意义上提升了笔记本的续航，但随之而来的就是OBS捕捉不到屏幕画面而彻底黑屏，所以根据Optimus的原理，我们要让OBS强制运行在核显上。

![session-attachment-2020-03-16-085157.jpeg](https://i.loli.net/2020/03/16/5O1dyXYD2qTJMFP.jpg)

## 显卡设置中设置强制集成显卡

以N卡为例，在右下角托盘找到Nvidia图标，然后显卡驱动中找到管理3D设置-程序设置，添加OBS进来后将其的模式改为集成显卡。

![Snipaste_2020-03-16_08-29-06.png](https://i.loli.net/2020/03/16/Wen52kqViEShBHO.png)


## 在Windows系统设置里面将OBS设置成“节能”

**这是Win10 1903新出的设置**，在设置-显示里面寻找图形设置，选择经典应用，添加OBS Studio并将其设置为“节能”，亲测不开这个选项仍然会导致黑屏并且难以直接用核显打开OBS。

![Snipaste_2020-03-16_08-28-08.png](https://i.loli.net/2020/03/16/3qi591mcAGCjv2b.png)

![Snipaste_2020-03-16_08-28-25.png](https://i.loli.net/2020/03/16/isptSqgCBLeX261.png)


## 更新到最新版的OBS

亲测，OBS最新版完美兼容win10 1909，不需要开启兼容模式，所以建议大家直接去[官网](https://obsproject.com/) 下载最新版应用来使用。

以上设置后OBS的黑屏问题应该已经解决（主要是网上的教程都没写第二步所以拿来水一下下）
