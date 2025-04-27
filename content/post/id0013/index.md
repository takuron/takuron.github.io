---
title: 多系统环境下Windows系统更新错误的一种解决方法
description: 多系统环境下Windows更新总是失败？尤其是有独立引导分区的电脑？别担心！本文记录了博主亲身经历的Win10更新血泪史，揭示了隐藏在非直接引导背后的更新障碍。通过临时调整引导方式，终于成功攻克更新难题！告别烦人的错误代码，让你的Win10顺利升级！
slug: id0013
date: 2019-12-13 00:00:00+0000
image: https://i.loli.net/2020/02/07/BuEFfQyZ7xJ2eRG.jpg
categories:
  - Windows
tags:
  - Windows
  - Linux
  - Windows更新
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

今天在各路大佬的洗脑之下，终于决定更新一下Windows10 1909，不过我的电脑一直处于更新失败被我屏蔽更新的状态，所以我准备一上午彻底解决Windows的更新问题，然后......

> 垃圾微软，菜鸡Windows，毁我青春。

首先先说明一下我电脑的状态，曾经装过多系统，留有专门的一个fat32格式的引导分区，不过现在只能引导到Windows了。系统自带的更新总是在最后一刻无法完成更新并撤销，而且不打这些安全补丁是不允许大版本升级的。

由于有错误代码，我一开始在怀疑我的.NET框架，而且为了安装学校的旧版SolidWorks 2010还安装过很多以前版本的.NET框架，所以就卸载了一遍重新安装，未果。清空升级缓存文件，未果。

然后动起了其他主意（后来发现这个选择非常对），使用官方的升级工具升级，然后：

![6897991-6802a8f578da9a07.png](https://i.loli.net/2020/02/07/Bkpzi4RgIMqJPdD.png)

然后又试了其他问题，并且尝试直接使用u盘pe来覆盖Windows文件，结果反正就是官方回退更新或者难以确定。然后我看到了[这个帖子]https://tieba.baidu.com/p/5078424060?red_tag=q1629212285) .

----

首先感谢上面那位贴吧大佬，个人亲测成功的土办法就是：改成Legacy+MBR直接引导到Windows。

装过多系统的各位应该都跟我一样，有一个专门的引导分区来完成多系统切换或者防止Linux的主分区过大而无法启动，但是这就带来了个问题：**Windows无论如何都不是直接通过主板的bios打开的**。具体可以在搜索里面输入 msconfig 查看：

![6897991-d9851dd2476d945f.png](https://i.loli.net/2020/02/07/5A9VnDE76rp8wMf.png)

要是里面是空的就说明你引导有问题，而且 **zz Windows会因此无法正常完成更新**，所以解决办法就是：在pe里面利用工具将分区格式改成mbr。

我使用的是微pe，原帖推荐的DiskGenius是个免费版没办法转换分区，我用的另一个分区工具成功转为mbr并先默认设置c盘微启动引导，

然后就可以正常使用[微软官方win10升级工具](https://www.microsoft.com/zh-cn/software-download/windows10)升级了。

有人说那我的引导分区怎么办，多系统怎么办？

**你不会再改回UEFI+GPT吗**，然后重建你的引导，这对给Linux分过区的你来说不难吧。
