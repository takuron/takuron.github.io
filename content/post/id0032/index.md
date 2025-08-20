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

## 美化与完善

### 语言与地区设置

- [简体中文本地化 - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%E6%9C%AC%E5%9C%B0%E5%8C%96)

我们按照以上教程先配置一个英文的全局local来防止tty中的中文渲染错误，修改`` /etc/locale.conf``：

```
LANG=en_US.UTF-8
```

然后分别编辑以下文件：

- ``~/.bashrc``
- ``~/.xinitrc``
- ``~/.xprofile``

为：

```
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
```

### 中文字体完善

- [字体配置 - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/%E5%AD%97%E4%BD%93%E9%85%8D%E7%BD%AE)
- [字体配置/中文 - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/%E5%AD%97%E4%BD%93%E9%85%8D%E7%BD%AE/%E4%B8%AD%E6%96%87)

接下来我们修改一下字体的配置，让默认字体更接近Android原生的字体风格。首先下载我们需要的字体，这里等宽字体我选择了新的[Maple Mono](https://github.com/subframe7536/Maple-font)，其可以将中文设置成拉丁字母双倍宽度来实现中英混合的情况下的等宽，比强行压缩汉字的更纱黑体效果好太多。其中如果maple mono系列如果遇到需要选择的情况请选archlinuxcn源。

```
paru -S ttf-roboto noto-fonts noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts ttf-dejavu ttf-maplemono-nf-unhinted ttf-maplemono-nf-cn-unhinted
```
然后我们修改`~/.config/fontconfig/fonts.conf`，以下是我根据配置建议写的Android字体配置，其中为了美观仍然选择的noto sans ckj sc为中文字体，并改用了maple系为等宽字体，这里也可以选择按上面网址里的配置修复Bug：

```
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <its:rules xmlns:its="http://www.w3.org/2005/11/its" version="1.0">
    <its:translateRule
      translate="no"
      selector="/fontconfig/*[not(self::description)]"
    />
  </its:rules>

  <description>Android Font Config</description>

  <!-- Font directory list -->

  <dir>/usr/share/fonts</dir>
  <dir>/usr/local/share/fonts</dir>
  <dir prefix="xdg">fonts</dir>
  <!-- the following element will be removed in the future -->
  <dir>~/.fonts</dir>

  <!-- 关闭内嵌点阵字体 -->
  <match target="font">
    <edit name="embeddedbitmap" mode="assign">
      <bool>false</bool>
    </edit>
  </match>

  <!-- 英文默认字体使用 Roboto 和 Noto Serif ,终端使用 Maple Mono NF.。-->
  <match>
    <test qual="any" name="family">
      <string>serif</string>
    </test>
    <edit name="family" mode="prepend" binding="strong">
      <string>Noto Serif</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>sans-serif</string>
    </test>
    <edit name="family" mode="prepend" binding="strong">
      <string>Roboto</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>monospace</string>
    </test>
    <edit name="family" mode="prepend" binding="strong">
      <string>Maple Mono NF</string>
    </edit>
  </match>

  <!-- 中文默认字体使用　Noto Sans CJK SC ,终端使用 Maple Mono NF CN。-->
  <match>
    <test name="lang" compare="contains">
      <string>zh</string>
    </test>
    <test name="family">
      <string>serif</string>
    </test>
    <edit name="family" mode="prepend">
      <string>Noto Serif CJK SC</string>
    </edit>
  </match>
  <match>
    <test name="lang" compare="contains">
      <string>zh</string>
    </test>
    <test name="family">
      <string>sans-serif</string>
    </test>
    <edit name="family" mode="prepend">
      <string>Noto Sans CJK SC</string>
    </edit>
  </match>
  <match>
    <test name="lang" compare="contains">
      <string>zh</string>
    </test>
    <test name="family">
      <string>monospace</string>
    </test>
    <edit name="family" mode="prepend">
      <string>Maple Mono NF CN</string>
    </edit>
  </match>

  <!-- Windows & Linux Chinese fonts. -->
  <!-- 把所有常见的中文字体映射到思源黑体和思源宋体，这样当这些字体未安装时会使用思源黑体和思源宋体.
解决特定程序指定使用某字体，并且在字体不存在情况下不会使用fallback字体导致中文显示不正常的情况. -->
  <match target="pattern">
    <test qual="any" name="family">
      <string>WenQuanYi Zen Hei</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Sans CN</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>WenQuanYi Micro Hei</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Sans CN</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>WenQuanYi Micro Hei Light</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Sans CN</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>Microsoft YaHei</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Sans CN</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>SimHei</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Sans CN</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>SimSun</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Serif CN</string>
    </edit>
  </match>
  <match target="pattern">
    <test qual="any" name="family">
      <string>SimSun-18030</string>
    </test>
    <edit name="family" mode="assign" binding="same">
      <string>Source Han Serif CN</string>
    </edit>
  </match>

  <!-- Load local system customization file -->
  <include ignore_missing="yes">conf.d</include>

  <!-- Font cache directory list -->

  <cachedir>/var/cache/fontconfig</cachedir>
  <cachedir prefix="xdg">fontconfig</cachedir>
  <!-- the following element will be removed in the future -->
  <cachedir>~/.fontconfig</cachedir>

  <config>
    <!-- Rescan configuration every 30 seconds when FcFontSetList is called -->
    <rescan>
      <int>30</int>
    </rescan>
  </config>
</fontconfig>
```

(未完待续)
