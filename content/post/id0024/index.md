---
title: 为Windows11设置更多DNS over https服务器&一键添加更多服务器脚本
description: 这篇文章讲述如何在Windows 10/11/Server 2022上开启系统级的DNS over Https (DoH) 功能。文章详细介绍了设置步骤，并提供了添加阿里、DNSPOD等国内常用DoH服务器的方法及实用脚本，让你轻松用上更安全的加密DNS服务！
slug: id0024
date: 2021-10-05 00:00:00+0000
image: https://img.takuron.com/20250827/ed8fda4fbfdea13178ac39ef421b2ebb.jpg
categories:
  - Windows
tags:
  - 网络
  - Windows
  - DNS
  - DNS over Https
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

早就有闻Windows系统已经添加了系统级的DNS over Https的支持，趁着此次重装Windows11新系统之际换掉AdguardHome改用Windows系统自己提供的DoH支持。

> 理论上Windows 10 build 19628以后的所有版本都支持此教程的方法，本人也亲测在Windows Server 2022上成功使用。

## 设置DNS over Https的位置

以WLAN网络为例：打开设置-WLAN-**硬件属性**-DNS服务器分配，选择手动。输入一个支持DoH的服务商的DNS地址（例如Google的8.8.8.8），下面灰显的选项就会启用：

![Snipaste_2021-10-05_14-20-37.png](https://img.takuron.com/20250827/19ab4d119bcd75a81aa2cf5d404cffc6.png)

![Snipaste_2021-10-05_14-19-54.png](https://img.takuron.com/20250827/53f7b46711870002bf18aa4c337fb20f.png)

> 2021.10.29 这不是一个BUG，这里设置的话可以自动覆盖所有将要连接的WIFI，更好的保护连接公共WIFI带来的隐私问题。
> <s>2021.10.5 你可以在选择网络连接的位置发现同样的选项来设置网络连接的属性，但是不能启用DoH，本文来自Takuron点top，这应该是Windows11的一个BUG。</s>

不过我们的设置并没有结束，**坑爹的微软只预置了一些常见的全球DoH服务器，但众所周知天朝不在地球上**。设置其他服务器不会有任何显示：

![Snipaste_2021-10-05_13-43-47.png](https://img.takuron.com/20250827/06670b3bfb0f38aaaeca8a6af77f72b8.png)

你只能直接设置以下DoH服务器：

- Cloudflare(1.1.1.1/2606:4700:4700::1111)
- Google(8.8.8.8/2001:4860:4860::8888)
- Quad9(9.9.9.9/2620:fe::fe)

如果需要安装国内常用的DoH服务器需要先添加

## 为系统添加更多DNS over Https服务器

我们需要注册更多的Doh服务器，这可以用以下命令解决（感谢[如何在win10中自定义doh服务器-远景论坛-微软极客社区](https://bbs.pcbeta.com/viewthread-1877659-1-1.html)：

````
netsh dns add encryption server=<your-server's-IP-address> dohtemplate=<your-server's-DoH-URI-template>
netsh dns show encryption server=<your-server's-IP-address>
````

例如添加阿里的DoH代码：

````
netsh dns add encryption server=223.5.5.5 dohtemplate=https://dns.alidns.com/dns-query
netsh dns show encryption server=223.5.5.5
````

IPV6同理，不过DoH服务商那么多，还要连着主要次要ipv4/v6都要添加，怕不是一行行打手都麻了，为此我专门写了一个脚本，自动添加了以下DoH服务商：

- 阿里DNS(223.5.5.5/2400:3200::1)
- DNSPOD(119.29.29.29/2402:4e00::)
- 深信服下一代网络工程IPV6 DoH(仅IPV6:240C::6666)
- 360(仅IPV4:101.226.4.6/123.125.81.6)
- Adguard(包括多种，[详细官网查看](https://bbs.pcbeta.com/viewthread-1877659-1-1.html))

下载后**请使用右击，以管理员权限运行**,[蓝奏云下载地址（提取码h6e1）](https://takuron.lanzoui.com/b0as59z2f)

可以看到添加后就能正常使用国内的DoH服务了。

![Snipaste_2021-10-05_13-45-26.png](https://img.takuron.com/20250827/a06762e22f8c2ba77b4d642eaa0bd5a0.png)
