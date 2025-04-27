---
title: 关于我AUR无法更新最终定位到git代理设置的事情
description: 在使用clash时请优先为git设置http代理，为git使用socks5代理会导致部分网站（aur.archlinux.org）产生TLS证书错误问题。
slug: id0030
date: 2025-04-06 00:00:00+0000
image: cover.jpg
categories:
  - Git
  - 软件使用
tags:
  - ArchLinux
  - AUR
  - Paru
  - Git
  - Clash
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

> 太长不看版本：在使用clash时请优先为git设置http代理，为git使用socks5代理会导致部分网站（aur.archlinux.org）产生TLS证书错误问题。

在之前使用Paru更新的时候遇到任何网络连接问题我都直接开开开全局劫持代理就完事了。这次是由于我自己用的小猫咪版本更新导致TUN模式的系统服务老是有问题无法使用才发现的一个错误，我在使用paru更新AUR的PKGBUILD时候爆出了这样的错误：

```
致命错误：无法访问 'https://aur.archlinux.org/vscodium-bin'：TLS connect error: error:00000000:lib(0)::reason(0)
```

首先还是系统代理开开开，看到仍然报错后还怀疑过paru无法正常读取自动设置的系统代理故而尝试手动设置命令行代理/尝试更换yay更新均无效。仍然同样报错，一度怀疑系统时间和本地证书问题，百思不得其解。

思索的时候重新审视报错日志发现卡在的位置实际是从aur.archlinux.org克隆PKGBUILD的时候，这个过程正常是使用git的，实际报错的为如下指令：

```
git clone --no-progress -- https://aur.archlinux.org/vscodium-bin
```

如果使用git那我给git手动设置了代理服务器，这样就能解释的通为什么错误不会因系统代理而变化，重新审视了我的git的配置文件（~/.gitconfig ）：

```
[http]
        proxy = socks5://127.0.0.1:7897
[https]
        proxy = socks5://127.0.0.1:7897

```

仍然没有问题，尝试验证性的从github克隆库没有问题，再尝试用设置同样代理的Firefox打开aur.archlinux.org网站也没有问题。也就是说问题准确定位是**git无法在通过代理的情况下验证特定网站（aur.archlinux.org）的TLS证书**？

![?](80a7c383d3c7a3fb47d28c89aad71f64_MD5.jpg)

重新以git代理\TLS错误联合aur等关键词搜索，看到了[在与clash配合使用的情况下有些机器在git使用socks5代理会存在问题](https://www.cnblogs.com/searchstar/p/18437537)，故尝试**将代理改为http**，修好了：

```
[http]
        proxy = http://127.0.0.1:7897
[https]
        proxy = http://127.0.0.1:7897

```

![?](80a7c383d3c7a3fb47d28c89aad71f64_MD5.jpg)