---
title: 9102年末的个人文件的管理
description: 还在为文件杂乱无章烦恼？看看我是如何利用多种资源，构建高效个人文件管理体系，告别微信传文件，随时随地找到你的文件！
slug: id0014
date: 2019-12-25 00:00:00+0000
image: https://i.loli.net/2020/02/07/ZIndB7rYHxcJEP5.png
categories:
  - 软件使用
tags:
  - 软件使用
  - Onedrive
  - Webdav
  - 坚果云
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

**本人一纯正薅羊毛党，以下内容会涉及一些占用各种（不属于本人的）资源的事情，精神洁癖请主动远离。**

> 学计算机救不了没了微信就没法传文件的人。  ——鲁迅

估计各位刚入大学的萌新会跟我一样，发现大学最大的麻烦就是：13事多。毕竟高考前不会有人闲的去找高三党做这做那的，但无所事事的大一新生最适合下方工作了。不过这还没完，即使在即将欢送9102年的时候，统计表格的工作在各位老师和班干部的眼里还是这么做的：

“大家把表格填一下，回头微信把自己填完的私发给我。”


![Screenshot-20191225-091439-01.jpg](https://i.loli.net/2020/02/07/PkUm7B4W5afrTdg.jpg)

（把全班的整理到一起估计也是逐行复制吧）

扯远了，不过这的确在侧面反应出大学生的文件多，设备多，且很多人还在用上世纪的方法进行着管理（或者压根不管理）。这个时候合理的文件管理才能保证我这种人的高效（zhe）率（teng）学习和生活。

## 文件分级

---

根据重要程度和私密程度进行分级，这在企业文件管理中非常重要，但是对于个人（尤其是大学生）来说就没有那种重要的感觉，毕竟接触不到什么机密。所以这里我认为**不缺钱随便存，买够正经云空间统统往里面塞是最枯燥的**。不过有钱估计看到标题就不往下读了，所以我给我的文件分为三类：

1. 隐私有关的纯文本笔记和除了office套件使用以外的文档，一些（加密后的）密钥文件。
2. 隐私有关的图片，音频，视频。
3. 隐私无关文件。

这是我的分级，当然各位会比我做得好。

## U盘

---

U盘虽然蠢但有用，这是相对于云端存储更可靠，相对于硬盘更便携的数据存储方案，我用的一个64g的，用来处理经常要修改的文档，并且以此为基准修改云端备份。在机房或者某些连不上网的电脑上面，你会感激你的u盘的。

## 坚果云

---

这个还是要强力推荐的，webdav协议具有极大的扩展性，配上一些开放的标准（例如keepass，markdown笔记），可以实现自由的多端协作，完爆其他国产云存储十条街，比海外网盘也好不好。不过弊端也很明显：免费版的流量限制，当然这是必须的，毕竟要恰饭的吗。

所以我用坚果云存储第一类文件，因为这些文件都很小（纯文本为主），不会像照片一样一旅游照片备份半个月流量没了（说的我经常旅游一样），并且访问速度足够快，不会像onenote一样抽风起来只能拜天求佛。

这里我密码管理可以参考[这篇文章](https://sspai.com/post/55403)，笔记本我用的[Joplin](https://joplinapp.org/)，他们都可以通过坚果云完成多设备同步，方便随时查看。

## 个人Onedrive

---

我有三个onedrive（会一个个介绍），先说个人的，这是最安全的存储区域，除了阴谋论怀疑微软，基本可以保证数据是安全可控的，可以存储一些隐私和重要资料，并且空间也是相对较大的15g（别问我多的10g哪儿来的，去淘宝买个扩容一起薅巨硬羊毛正解，这个比较靠谱）。不过缺点还是有的：还是不够用，毕竟128g的手机都能被各位塞的满满当当的。

这里我主要保存个人照片（同步方式很多），还有一些比较大的非纯文本文档（例如PSD文件，也许更应该归类于图片）。

电脑的话如果要上传直接用win10自带客户端同步，手机这里推荐一下[FolderSync](http://www.tacit.dk/)，可以定时自动同步一些文件夹，可以保持文件多设备同步。

## 学校Onedrive

---

来自一个拖各大平台平均水平的大学，学校onedrive没有管理员所以容量是1T。这个不是绝对安全的，因为学校有可能设置管理员或者收走你的学生邮箱，但是一般情况下这也算是个人空间。不过这个网盘还有个问题，为了防止过度分享，没有管理员的onedrive没法接入像上文FolderSync这样的同步软件，难以用来在设备之间同步文件，这就很头疼。我最后的解决方案是利用他做敏感数据冷备盘，定时手动加密数据压缩传进去，并且把一些学习有关的隐私数据放在里面。

## 5T子账号的Onedrive

---

不讲具体账号类型，反正不靠谱就完事，但一般那种有管理员权限的个人开放申请的基本都不会跑路，有点良心的也不会经常看你数据，还算是长期有效。这种盘的价值就在于空间足够大，而一般占用空间的也都是一些网上可以公开下载的软件，游戏或者小姐姐。并且这是可以用FolderSync的，所以它理所应当成为了一些非重要数据的桥梁和备份，壁纸啊，视频啊，color图啊。随叫随到，多端备份。

甚至可以开[分享网站](https://res.takuron.top/)

## 其他的备份

---

当然，有人得说了，微软跑路我不得凉凉？首先，我电脑手机分别本地都有备份，而且……


![IMG-20191225-153328-01.jpg](https://i.loli.net/2020/02/07/DcbBsIhJu1P3285.jpg)

冷盘备份了解一下。

## 总结

---

经过不懈的努（zhe）力（teng），现在终于做到哪儿都能找到备份文件了，251试炼能活下来了（跑）。基本上找我存过的文件可以做到在任意设备三十秒内找到，合理的文件管理会给寻找文件带来方便的同时还能边喝水边看微信找文件的同学。算是一次有成果的折腾
