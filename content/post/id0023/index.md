---
title: 使用开源的Gadgetbridge管理小米手环4
description: 告别小米运动APP广告！转投开源Gadgetbridge，为你的小米手环带来纯净体验。本文提供详细安装步骤，助你摆脱官方束缚，享受无打扰的智能手环生活。
slug: id0023
date: 2021-04-10 00:00:00+0000
image: https://i.loli.net/2021/04/10/em5ysORGpYJHola.png
categories:
  - 科技产品
tags:
  - 科技产品
  - 小米手环
  - 穿戴式
  - 软件使用
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## 前言

小米手环已经更新到了6，但越看更新越没有换的欲望，手环的功能到现在为止都没有突破性的改变，而手表还是一如既往的耗电。反而是小米运动APP（其实应该是华米的运动APP吧）的广告越来越遭人烦，这让我的视线逐步向第三方的方案转移。虽然在此之前有同为精简第三方的Notify & Fitness for Mi Band，但留一个破解本体还是让人难免不舒服，于是这次转而使用开源的Gadgetbridge，而且说句实话，在整理好的APP帮助下个人认为体验是最好的。

## 安装步骤

[备用下载位置](https://takuron.lanzous.com/b0armx34f)，密码：cug3

1. 在官方APP中将手环的账号绑定（完成后可卸载）： ![photo_2021-04-10_14-47-16.jpg](https://i.loli.net/2021/04/10/2W3lUxhYoSRyiHA.jpg)
2. 下载[huafetcher](go?url=https://codeberg.org/vanous/huafetcher)用于获取加密key 。
3. 在第二项选择为xiaomi：![photo_2021-04-10_14-47-38.jpg](https://i.loli.net/2021/04/10/TPrMg1OHoKb6RL8.jpg)
4. 选择Get token进入网页，登录小米账号（这里注意修改手机号为+86）：![photo_2021-04-10_14-47-47.jpg](https://i.loli.net/2021/04/10/pzZSOuPiwDX6HqY.jpg) ![photo_2021-04-10_14-47-51.jpg](https://i.loli.net/2021/04/10/lyeKfqND7PRovrV.jpg)
5. 这里会报错，这是正常的，直接复制地址栏所有内容到URL result内：![photo_2021-04-10_14-47-57.jpg](https://i.loli.net/2021/04/10/j3o2SiMUryEH7IX.jpg) ![photo_2021-04-10_14-48-02.jpg](https://i.loli.net/2021/04/10/hsCqDYbkFyHPUun.jpg)
6. 选择“Fetch key”，把下方生成的key复制走（到这里这个APP可以卸载了）
7. 下载[Gadgetbridge](go?url=https://codeberg.org/Freeyourgadget/Gadgetbridge)，进入后点击右下角的“+”，然后搜索你的小米手环：![photo_2021-04-10_14-48-15.jpg](https://i.loli.net/2021/04/10/3nitAx8zl6vePhp.jpg)
8. 这里长按进去，拖到底下，找到认证密钥，输入你刚刚复制的key到里面，回去连接，就可以正常使用了。![photo_2021-04-10_14-48-19.jpg](https://i.loli.net/2021/04/10/Ww3GpNkdzHTuvPD.jpg)

之后只要不动官方APP内的绑定关系就能一直使用了。

## 优劣势对比

与小米手环官方APP和小米手环专用的Notify & Fitness for Mi Band比起来，Gadgetbridge有以下优势：

- **开源  免费（相对Notify & Fitness for Mi Band） 无广告（相对官方）**
- 通用方案，可以同时兼容其他品牌的手环。
- 轻便优雅，官方APP不需要修改不需要保留给爷爬。

那么，代价是什么呢？

- **缺少对心跳、月经（米环5）等小米手环特色功能的支持**
- 更换主题不方便（这个可以用官方APP当工具人）
- 通知管理不太符合个人的使用习惯（个人一般喜欢设置仅聊天手环通知，而这个应用默认·是黑名单模式）


## 总结

AD米一生黑，开源万岁。
