---
title: 2025年将Windows10 LTSC压缩来实现DVD单盘刻录
description: 针对Windows 10 LTSC 2021镜像体积超过普通DVD容量的问题，本文详细记录了如何使用DISM命令将install.wim压缩为install.esd，成功将ISO体积缩减并实现单盘刻录的完整步骤。
slug: id0035
date: 2025-12-01 00:00:00+0000
image: https://img.takuron.com/20251201/4925728ab6c82e56bd49659f7e013dde.webp
categories:
  - Windows
tags:
  - Windows
  - DISM
  - 镜像封装
  - DVD刻录
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

前几天收到了一个玩具：塔式的戴尔OptiPlex7070，配置为i5 9500 + 16g内存，一块2T的机械硬盘外加一个DVD光驱，想了一下给他加了个gtx 1650和Nvme固态浅浅试用一下商用机的感觉。

![f1214a43cdeedcc7e6421a287a00077e.webp](https://img.takuron.com/20251201/f1214a43cdeedcc7e6421a287a00077e.webp)

但是装完后买完DVD就后悔：每G存储的价格相比于机械硬盘并没有优势的同时，4.3G每盘的容量却很尴尬：存大文件需要分片的同时还不能多次读写，这个容量甚至无法写入一个Windows10的安装媒介……吗？

## 2025年Windows镜像现状

很早以前微软一直会将Windows镜像控制在4.3G的红线来方便DVD用户。然而随着U盘安装的普及，自2019年开始微软已经不再为此专门控制镜像的大小并推荐使用8G以上的U盘作为安装媒介。截止本文发布（2025年12月）最新的Windows11镜像约为7.32G，即使考虑使用光驱的为老电脑为其制作一个Windows10 LTSC 2021的镜像,大小依然有4.9G，故而我们需要方法压缩。

[【无用经验】系统镜像太大，如何单盘刻录？ --Bilibili](https://www.bilibili.com/opus/997436412861087744)这篇文章给出了一些方案，包括但不限于UltraISO自带压缩（基本压不了多少）、后安装补丁（LTSC版本不带补丁）、换盘（没这么好的设备）、装完改版本（强改多麻烦啊）等，得出结论对于特定的LTS系统（例如文中提出的Windows Server）无解，真的是这样吗？

## 使用dism命令压缩esd来减少体积

[ISO镜像文件太大怎么刻录_windows区_软件区 卡饭论坛 - 互助分享 - 大气谦和!](https://bbs.kafan.cn/thread-2145662-1-1.html)这篇帖子提到的压缩install.wim是一个有效的方法，实测使用后成果压缩了接近1G的体积成功安装，这篇帖子讲的比较粗略下面是详细步骤：

### 下载Windows10 LTSC 2021

随着2025年10月14日微软停止对Windows10的支持，现在仍处于支持周期的旧版Windows10只剩下了Windows10 LTSC 2021的普通（截止到2026年）和iot版本（截止到2031年），考虑仍需要Windows10的多数为旧电脑，故均推荐直接使用LSTC版本。

建议前往[微软评估中心（已失效）](https://www.microsoft.com/zh-cn/evalcenter/download-windows-10-enterprise)或者[NEXT, ITELLYOU](https://next.itellyou.cn/)下载，这里转载一个磁力链接（这个发布至今都没有更新，应该也许可能大概不会再变动了吧）

```
magnet:?xt=urn:btih:366ADAA52FB3639B17D73718DD5F9E3EE9477B40&dn=SW_DVD9_WIN_ENT_LTSC_2021_64BIT_ChnSimp_MLF_X22-84402.ISO&xl=5044211712
```

### 提取并压缩install.wim

接下来使用无论用Windows自带的挂载磁盘镜像也好还是7zip等软件的解压也好，寻找sources/install.wim文件，这应当是整个镜像里面最大的文件，包含后续要提取安装的绝大多数内容。

接下来我们使用dism对这个镜像重新压缩成esd格式，首先以管理员模式打开powershell，输入以下命令：

```
dism /Get-WimInfo /WimFile:【解压出来的install.wim目录，例如D:\Win10_Work\install.wim】
```

此时会输出镜像的所有版本（包含家庭版、专业版等），对于常规Windows版本，我们需要选择一个特定的版本来压缩。但正常我们这个目录应当只有一个版本。

接下来我们重新压缩这个文件，输入以下命令：

```
dism /Export-Image /SourceImageFile:【install.wim目录】 /SourceIndex:1 /DestinationImageFile:【将要输出的install.esd目录】 /Compress:recovery
```

接下来只需要等待，按帖子那位老哥所说需要很久，但我的R9 9900x确实几分钟就压缩好了（跑

![4cdded6d5db20cf415e4643cd693e874.webp](https://img.takuron.com/20251201/4cdded6d5db20cf415e4643cd693e874.webp)

### 重新打包新的ISO并验证

使用UltraISO打开原本的ISO，删除其中的sources/install.wim来将其替换成sources/install.esd，然后导出ISO即可，可以尝试先将这个ISO由Rufus写入U盘并测试。只要能走到“勾选我接受许可条款”这一步，或者能让你选择“自定义安装”并看到硬盘分区列表，测试就通过了。

> 这里以后可能会发我做好的可以直接DVD单盘刻录的ISO镜像下载链接。

测试完毕后就可以刻录光盘了。