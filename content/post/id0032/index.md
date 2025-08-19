---
title: ArchLinux折腾记录1-安装
description: 本系列用于聚合本人安装使用ArchLinux过程中遇到的一些问题。第一篇就从重新安装ArchLinux的记录开始。
slug: id0032
date: 2025-08-19 00:00:00+0000
image: https://img.takuron.com/blogpost/QHtj4iXrN6yjjmP6.webp
categories:
  - ArchLinux
tags:
  - Linux
  - ArchLinux
  - 协同开发
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

本系列用于聚合本人安装使用ArchLinux过程中遇到的一些问题。第一篇就从重新安装ArchLinux的记录开始，本人主要的安装过程请参考[archlinux 简明指南](https://arch.icekylin.online/)，本文也会以其的指导为基础主要列出一些本人的一些个性化配置。

## 基础安装

- [archlinux 基础安装 | archlinux 简明指南](https://arch.icekylin.online/guide/rookie/basic-install)

基础安装部分其实没有什么个性化的内容。个人其实认为目前随着Windows的安全机制完善，双系统反而破坏了Windows的一些安全特性，故本人更建议直接全新安装。

### Nvme硬盘的先进格式化与4kn模式

参考：

- [先进格式化 - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/%E5%85%88%E8%BF%9B%E6%A0%BC%E5%BC%8F%E5%8C%96)
- [教你把 NVMe SSD 切换到原生4K模式 - 知乎](https://www.zhihu.com/tardis/zm/art/355590811?source_id=1005)

2025年的绝大多数Nvme硬盘和部分企业级机械硬盘已经使用了更先进的原生4K物理分区，但为了保证向下兼容性，几乎全部消费级的盘默认开启的都是模拟远古512b扇区的模式，这样理论上会产生逻辑扇区和物理扇区的对齐问题导致性能损失(不过实际对于消费级的低负载基本无影响)。 出于强迫症，既然正好要格式化硬盘就顺便把这个改了。

首先查看所有硬盘和分区：

```
# lsblk --fs

NAME   FSTYPE   LABEL       UUID                                 MOUNTPOINT
sda
├─sda1 vfat                 C4DA-2C4D                            /boot
├─sda2 swap                 5b1564b2-2e2c-452c-bcfa-d1f572ae99f2 [SWAP]
└─sda3 ext4                 56adc99b-a61e-46af-aab7-a6d07e504652 /

```

选择需要的硬盘（nvme0n1为例），查找支持的格式化后逻辑块地址大小（Formatted Logical Block Address Size, FLBAS）。

```
# nvme id-ns -H /dev/nvme0n1 | grep "Relative Performance"

LBA Format  0 : Metadata Size: 0   bytes - Data Size: 512 bytes - Relative Performance: 0x2 Good (in use)
LBA Format  1 : Metadata Size: 0   bytes - Data Size: 4096 bytes - Relative Performance: 0x1 Better

```

找到可用的LBA Format后使用以下命令格式化并修改逻辑分区大小，其中-l是你选的对应模式：

```
nvme format /dev/nvmeXn1 -l 1
```

## 显卡驱动与桌面环境

- [archlinux 显卡驱动 | archlinux 简明指南](https://arch.icekylin.online/guide/rookie/graphic-driver.html)
- [桌面环境与常用应用安装 | archlinux 简明指南](https://arch.icekylin.online/guide/rookie/desktop-env-and-app.html)

这里相比于原教程有比较大的出入，我使用的是Gnome作为默认桌面。Gnome对于高分缩放更加友好的同时界面比较好看，虽然Gnome的大而全完全就在ArchLinux的对立面，但给予用户选择大而全也是一种灵活自由吧。（跑

### 软件安装的一些小调整

我将Aur管理的安装放到前面且改用了Paru，在启用archlinuxcn源后即可：

```
sudo pacman -S archlinuxcn-keyring
sudo pacman -S paru
```

再就是中文字体的安装可以调的更前面一点来避免gnome和浏览器出现方框字：

```
sudo pacman -S adobe-source-han-serif-cn-fonts wqy-zenhei 
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra 
```

### Gnome和中文输入法

- [ArchLinux安装GNOME桌面 - Skyone Blog](https://blog.skyone.dev/2023/archlinux-gnome/)
- [ArchLinux-GNOME的安装、配置和美化 - 简书](https://www.jianshu.com/p/9f5710d454b6)

安装xorg

```
sudo pacman -S xorg xorg-server xorg-xinit
```

安装gnome，其中gnome里面可以全装（你都选gnome了也不会太在乎这些基础功能是否精简干净了吧），但gnome-extra里面有大量没用的游戏、效率工具等内容，可以直接选择不装或者只挑有用的进行安装，一定一定要挑一下别学我直接一口气给我装了十个游戏。

```
sudo pacman -S gnome gnome-extra
```

然后打开gdm就可以显示图形界面了：

```
sudo systemctl enable --now gdm
```

安装完成后若在设置里面没有显示中文请确定按简明教程正确配置``/etc/locale.gen``后执行：

```
sudo locale-gen
```

然后安装rime-ibus，选择ibus的原因是Gnome的窗口管理器基本和ibus输入引擎深度绑定，与其去折腾fctix的各种奇怪bug不如直接把朙月输入法的引擎引入ibus来的省心。

```
sudo pacman -S ibus-rime
sudo pacman librime-data-luna-pinyin
```

(未完待续)