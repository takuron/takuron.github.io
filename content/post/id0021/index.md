---
title: 【个人渣翻】Don't kill my app!——一加
description: 翻译自Oneplus Don’t kill my app!，遵循CC-BY-4.0 License。
slug: id0021
date: 2020-07-19 00:00:00+0000
image: https://i.loli.net/2020/07/19/IcApJi58xgCLk2D.jpg
categories:
  - 杂谈
tags:
  - 翻译
  - 杂谈
  - 软件
  - Oneplus
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

> 翻译自[Oneplus \| Don’t kill my app!](https://dontkillmyapp.com/oneplus)，遵循CC-BY-4.0 License。
> 本人英语水平渣，只是为翻译自用。欢迎友善提出翻译错误，不喜勿喷。

## 背景

一加和其基于Android修改的氧OS以最大程度提升原版Android的体验而著称。也许在用户体验方面的确是这样，但其在后台进程限制方面的表现却与之完全相反。

当他们发布一加5和一加6手机时，一加推出了迄今为止市场上最严厉的后台限制，这甚至使小米或华为相形见绌。用户不仅需要启用额外的设置才能让应用正常运行，而且这些设置甚至会随着固件更新而被重置，应用会再次崩溃。因此用户被要求定期重启这些设置。

## 为用户准备的解决方法

### 电池选项

- 关闭**设置>应用和通知>需要操作的应用>高级>电池优化**选项。
  **警告**：最近一加手机开始对一些应用随机恢复这个设置。也就是说如果你设置为未优化，第二天可能就会恢复到优化状态。
  为了避免系统自动恢复成未优化，你还必须将应用锁定到 “最近应用 ”列表中。启动您要保护的应用程序，进入最近的应用程序（应用程序切换器），点击应用右上角的锁定按钮。（译者注：新版系统请长按应用窗口>锁定）
  这将避免应用程序在后台被杀死，并防止系统恢复电池优化设置。
  然而，这并非100%。您可能需要每隔一段时间检查系统设置。请参阅[提交给一加的错误报告](https://forums.oneplus.com/threads/in-battery-optimisation-apps-are-getting-automatically-switched-from-not-optimised-to-optimised.849162/)。

- 设置**设置>电池>电池优化>切换到右上角菜单中的 "所有应用">需要操作的应用**为“**不优化**”。

注意：有网友表示，在一加及更早的版本的手机中，需要在开发者选项中禁用Doze模式。

（译者注：这两种方法目测是等效的，为二选一关系）

### 应用自启动

在一些一加手机上，应用程序自动启动基本上阻止了应用程序在后台工作，请为您的应用程序禁用它。

![ss_oneplus_1.jpg](https://i.loli.net/2020/07/19/ORNBSqeG6HKadV9.jpg)

### 高级优化

- 一加6和之后的手机：
  选择**设置>电池>电池优化>更多（三个点）> 高级优化**
  你会在那里看到两个选项。这两个选项都是默认启用的。
  深度优化（译者注：新版应该叫智能电池管理）
  这是主要的应用杀手，如果你需要任何应用程序在后台运行，请禁用它。
  睡眠待机优化
  一加手机试图学习你通常在什么时候睡觉，在这些时间里，它就会禁用手机的网络连接。这个设置会阻止推送通知的发送。

![ss_oneplus6_setting_sleepstandby.jpg](https://i.loli.net/2020/07/19/7zQfsauGBMUjIHd.jpg)

- 一加6之前的手机：
  关闭**设置>电池>电池优化>（三个点）>增强优化**

注意：这应该有助于解决您失去与智能手表/健身追踪器的蓝牙连接的问题（例如用于睡眠跟踪）。

### 近期的应用程序清理行为

一般情况下，当你滑动清除一个应用，它是不会关闭的。Android系统自己就能很好地处理这个问题。然而在一加手机上，这可能以不同的方式工作。最近的应用清除行为管理器可能会设置成杀死滑动清除的应用。

![ss_oneplus_2a.jpg](https://i.loli.net/2020/07/19/cMSf9ALVetz3siv.jpg)

## 为开发者准备的解决方案

暂无

## 了解更多

[Gadgethacks: Disable This Setting if Notifications Are Delayed on Your OnePlus](https://oneplus.gadgethacks.com/how-to/disable-setting-if-notifications-are-delayed-your-oneplus-0192639/)

## 徽章

![Snipaste_2020-07-19_15-07-45.png](https://i.loli.net/2020/07/19/IHB5tkvxnC4W9h3.png)

> 译者后记
> 一加的后台策略充满了与国内各大厂随地拉屎唤醒做斗争的辛酸，但也充斥着随便杀一下后台的不讲究。对于老外来说如此严格的后台管理策略是无法理解的，但对于国内的用户来说还是两难的选择，要不是大家都在用微信，谁还给垃圾腾讯机会呢？
