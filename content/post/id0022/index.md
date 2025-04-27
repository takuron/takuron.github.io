---
title: Windows系统联网验证策略和“去除叹号”
description: 在特定网络环境下，Windows可能误报“无Internet”连接，影响部分应用使用。本文将教你如何像解决安卓“叹号”问题一样，通过调整系统设置，让你的电脑正确识别网络状态，告别连接困扰。
slug: id0022
date: 2020-11-21 00:00:00+0000
image: https://i.loli.net/2020/11/21/VIA9Jxk4ROSF3XG.jpg
categories:
  - Windows
tags:
  - 网络
  - Windows
  - 软件使用
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

出于你懂得的的原因，在我朝连接国际互联网那叫一个感人。在原生安卓中，就是因为这个众所周知的原因无法正确判断联网状态诞生了“去除叹号”软件。同样的，作为难兄难弟的微软爷不甘示弱。这两天同样的事情发生在了我的Windows电脑上。

![Snipaste_2020-11-21_09-38-13.png](https://i.loli.net/2020/11/21/Pq9cWY3jedNg5nA.png)

在使用以下方法时，请首先确定这只是“显示错误”，即你已经可以判断你的电脑可以正常连接互联网的情况下还是如此显示并且影响到你对一些应用（例如自带应用商店）的使用。

所有的联网验证相关的参数都位于注册表`\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NlaSvc\Parameters\Internet`当中，这里以我正在使用的Windows10 20H2为例：

![Snipaste_2020-11-21_09-47-10.png](https://i.loli.net/2020/11/21/aYfUbPrgCdHkxTm.png)

这就很明了了，直接说解决方案。

## 直接关闭整个验证

如果你在用一个台式机，联网验证对你来说完全没用（自家路由器难道还有需要登录的？），那么你可以直接关闭这个功能，将`EnableActiveProbing`参数改为0即可。

## 验证DNS是否正常

如果你是笔记本，只想修好这个功能而不是一股脑的关死，那么电脑会首先确定你的ISP有没有给你的DNS投毒。以我目前的参数设置为例，在powershell里面查询：

```
nslookup dns.msftncsi.com
```

其中被查询的域名是你注册表中`ActiveDnsProbeHost`的值。Windows会验证DNS查询到的结果是否和`ActiveDnsProbeContent`的值相同，v6同理。如果你返回的是类似于127.0.0.1这种解析结果，恭喜你被DNS投毒了。本人遇到的就是这种情况（PS：阿里的DNS都能间歇性投毒我想说什么不用讲了吧），但我在用AdGuardHome，所以加个重写就解决了。

如果你没有用这类软件，并且你不想通过改写host这种自欺欺人的手段来解决问题，那么你可以将被查询的域名换成阿里dns的域名`dns.alidns.com`，大多数情况下，其结果总对应其DNS服务器IP`223.5.5.5`、`2400:3200::1`，可以将对应值修改后以此验证。

## 验证HTTP访问是否正常

然后电脑会确定你的HTTP服务是否正常，其会向`ActiveWebProbeHost`对应的服务器请求一个`connecttest.txt`的文本文件，然后确认其值是否与`ActiveWebProbeContent`相等。以我目前的配置为例，这相当于：

![Snipaste_2020-11-21_10-27-38.png](https://i.loli.net/2020/11/21/MQDkIs74zLWE6lp.png)

解决的方法有很多，DNS投毒同样会影响这个的验证结果（并且这种情况导致的验证失败非常常见），可以先尝试上述方法。

若仍无效或者是希望彻底解决问题，用gitee page自建一个测试服务器并不是一个完美的解决方案，因为无法判断ipv4联通还是ipv6连通，**此处待大佬解决**。

## 总结

整体来说微软的验证大多数时候还是有效的，如果偶尔出现了不可用的状况可以按照本文进行修改解决。

