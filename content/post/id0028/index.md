---
title: OPNsense使用记录02：第三方软件源和AdGuardHome配置
description: 本文介绍在OPNsense中添加第三方软件源，通过SSH安装并配置AdGuard Home。指导关闭自带DNS，启用AdGuard Home并设置上游DNS、缓存和广告过滤规则，实现全网高效广告拦截及DNS管理。
slug: id0028
date: 2024-11-27 00:00:00+0000
image: https://img.takuron.com/blogpost/KABjvaXbhSmWkCg.webp
categories:
  - OPNsense
tags:
  - 网络
  - 路由器
  - 组网
  - OPNsense
  - AdGuardHome
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

为了更好的管理家庭网络，我们可以为OPNsense添加更多类型的软件来实现更多功能。

## 添加第三方软件源

首先为了开启第三方源我们要启动ssh，这个选项可以在系统/设置/管理里面启用安全shell（不建议持续打开，在配置完成后建议关掉）。

![Snipaste_2024-11-17_10-48-38-tuya.webp](https://img.takuron.com/blogpost/73122fa124ce6702eda534cd9114d563_MD5.webp)

然后用你服务的的ip登录ssh，这里我选择的是Windows11自带的终端界面，PowerShell里面提供了ssh的登录程序我们直接敲命令登录即可，用户名密码即为你安装的时候的用户名和密码。

![Snipaste_2024-11-17_10-49-24.png](https://img.takuron.com/blogpost/39dc38a490cdaa7a0242afe0400f3b78_MD5.png)

输入如下命令：

```
fetch -o /usr/local/etc/pkg/repos/mimugmail.conf https://www.routerperformance.net/mimugmail.conf
```

然后更新缓存

```
pkg update
```

此时打开系统/固件/插件里面搜AdGuardHome，如果有显示即说明mimugmail源添加成功。

![Snipaste_2024-11-17_10-51-14-tuya.webp](https://img.takuron.com/blogpost/6d26f25f89b05784eeb659b86c3416ce_MD5.webp)

## 启动AdGuardHome

AdGuard Home 是一款全网广告拦截与反跟踪软件，安装在路由器后即可充当dns服务器对整个网络（通过dhcp设置dns）的设备的dns请求进行处理和广告过滤。

首先在上面那个界面点击加号安装AdGuardHome，下载完成后刷新网页，如果能在 服务 里面看到AdGuardHome即为安装成功。

![Snipaste_2024-11-17_10-52-11-tuya.webp](https://img.takuron.com/blogpost/d8bc2b27a83ed062d1d1deae33e6321e_MD5.webp)

在启用AdGuardHome之前我们先关掉自带的Unbound DNS，这样我们就可以直接让AdGuardHome监听53端口了。打开服务/Unbound DNS/常规，取消启用（这里我顺便给了个其他端口免得出奇怪问题）。

![Snipaste_2024-11-17_10-52-37-tuya.webp](https://img.takuron.com/blogpost/915826c96bcf8742f37a3b3930240c33_MD5.webp)

此时启用AdGuardHome，浏览器打开 你的网关ip:3000 的网页，就可以开始进行常规AdGuardHome配置了。

![Snipaste_2024-11-17_10-53-27-tuya.webp](https://img.takuron.com/blogpost/4bc2f9f10c12e938d3c93cce4f779854_MD5.webp)

## AdGuardHome个人配置

AdGuardHome网上的各种教程就比较多了，这里就简单分享下个人的配置方式。注意以上规则是适用于局域网dns分发的规则，自建dns服务和终端设备使用的契合度请自行设计。

dns上游这里我选择了思科的OpenDNS并且直接ip访问DNS over https，其没有污染、支持EDNS的同时访问速度亲测在国内还算过得去，不过为了提高效率建议手动拉大缓存容量并且开启乐观缓存（你不开很多上游实际也是乐观缓存，所以开就行了）,然后选择了阿里和DNSpod做后备,启用EDNS提高查询准确性。实测在乐观缓存条件下响应时间整体在50ms以内。

更多DoH服务器列表可以参考下[这个帖子](https://coding.gs/2024/06/09/available-doh/)

![Snipaste_2024-11-27_19-32-38-tuya.webp](https://img.takuron.com/blogpost/f7e0fa99d02c39acd9b9b0cdee08a16c_MD5.webp)

![Snipaste_2024-11-27_19-32-47-tuya.webp](https://img.takuron.com/blogpost/d947c07a23e0f4688b2eb3660bd92aec_MD5.webp)

![Snipaste_2024-11-27_19-33-14-tuya.webp](https://img.takuron.com/blogpost/8e1076559ff18657ed6fcd568b390cd4_MD5.webp)

广告过滤规则我选择的是[217heidai/adblockfilters](https://github.com/217heidai/adblockfilters)中的DNS拦截版本，只用开启这一个就好了。

