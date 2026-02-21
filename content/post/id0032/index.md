---
title: ArchLinux折腾记录01：安装
description: 本系列用于聚合本人安装使用ArchLinux过程中遇到的一些问题。第一篇就从重新安装ArchLinux的记录开始。
slug: id0032
date: 2026-02-21 00:00:00+0000
image: https://img.takuron.com/20250827/fd891f4ceba3bd23434784b7077c9044.webp
categories:
  - ArchLinux
tags:
  - Linux
  - ArchLinux
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

本系列用于聚合本人安装使用ArchLinux过程中遇到的一些问题。第一篇就从重新安装ArchLinux的记录开始，本人主要的安装过程请参考[archlinux 简明指南](https://arch.icekylin.online/)，本文也会以其的指导为基础主要列出一些本人的一些个性化配置。

> 2026/02/21 更新Gnome安装为wayland。

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

由于Gnome已经完全转向wayland，所以我们不再安装xorg，按以下命令安装 mesa 和 xwayland 用于兼容旧有的xwindow程序。

```
sudo pacman -S mesa xorg-xwayland
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

Gnome的ibus无法通过正常的``ibus-setup``里的配置进行添加和编辑，也无法检查常规路径里面大的配置文件，且gnome输入法设置里的设置又少的可怜。以为ibus设置横向选词为例，根据[这个帖子](https://github.com/rime/ibus-rime/issues/52)，正确的Gnome配套的ibus配置文件位于:

```
~/.config/ibus/rime/build/ibus_rime.yaml
```

打开这个文件后修改以下选项即可实现横向输入：

```
style:
   horizontal: true
```

## 美化与完善

- [可选配置（基础篇） | archlinux 简明指南](https://arch.icekylin.online/guide/advanced/optional-cfg-1.html)

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

添加如下内容：

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

```xml
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

### 安装雾凇拼音

- [Rime-ice配置 - ArchLinux](https://github.com/iDvel/rime-ice#arch-linux)
- [Rime 配置文件 - 補靪](https://github.com/iDvel/rime-ice#arch-linux)

雾凇拼音是 Rime 的一份配置仓库，用户需要下载 各平台对应的前端，并将此配置应用到配置目录。 雾凇拼音提供了一套开箱即用的完整配置，包含输入方案（全拼、常见双拼）、长期维护的开源词库及各项扩展功能。

通过以下命令安装雾凇拼音的文件：

```shell
paru -S rime-ice-git
```

编辑'$HOME/.config/ibus/rime/'文件，添加如下内容：

```yaml
patch:
  # 仅使用「雾凇拼音」的默认配置，配置此行即可
  __include: rime_ice_suggestion:/
  # 以下根据自己所需自行定义，非必须内容，仅做参考。
  # 针对对应处方的定制条目，请使用 <recipe>.custom.yaml 中配置，例如 rime_ice.custom.yaml
  __patch:
    key_binder/bindings/+:
      # 开启逗号句号翻页
      - { when: paging, accept: comma, send: Page_Up }
      - { when: has_menu, accept: period, send: Page_Down }
```

切换输入法到rime，等待重新构建输入方案后就可以使用雾凇拼音了。

### TCP调参

- [TCP 迷之调参 - 智能网络优化工具](https://www.omnitt.com/)

估计玩过VPS的都多少了解过TCP调参这个事情吧。虽然大多数在服务器上可以有效提高吞吐量的设置在终端设备上就是浪费内存浪费电，但有些算法类的调整（例如bbr拥塞控制）还是能有效提高设备网络性能的。

为``/etc/sysctl.conf``文件添加如下内容，主要是改变队列管理和拥塞控制算法其他都可动可不动：

```
# --- 笔记本网络与系统优化 ---

# 使用现代队列管理算法，对抗缓冲区膨胀，降低延迟
net.core.default_qdisc = cake

# 启用 BBR 拥塞控制算法以提升速度和吞吐量
net.ipv4.tcp_congestion_control = bbr

# 启用 TCP Fast Open 减少连接延迟
net.ipv4.tcp_fastopen = 3

# 允许重用 TIME_WAIT 状态的连接
net.ipv4.tcp_tw_reuse = 1

# 连接空闲后更快地恢复传输速度
net.ipv4.tcp_slow_start_after_idle = 0

```

### Gnome插件推荐

为了给gnome安装插件，首先需要安装gnome-shell-extensions和gnome-browser-connector，如果仍然无法安装请再安装对应浏览器里的插件。（和其他发行版不同**不需要安装chrome-gnome-shell**）

```
paru -S gnome-shell-extensions gnome-browser-connector
```

以下是本人在用的一些插件：

- [AppIndicator and KStatusNotifierItem Support ](https://extensions.gnome.org/extension/615/appindicator-support/)：恢复被gnome取消的托盘
- [Vitals](https://extensions.gnome.org/extension/1460/vitals/): 清爽的硬件监控

## 常用软件

- [Linux 日常操作与基础知识 | archlinux 简明指南](https://arch.icekylin.online/guide/advanced/system-ctl.html)

### 防火墙

按照个人使用习惯安装ufw防火墙和其gui版本gufw并启动：

```
paru -S ufw gufw
sudo systemctl enable --now ufw.service
sudo ufw enable
```

这个防火墙不用我多介绍了吧无论cli还是衍生的gui都是最简单的那一类了。注意放行一些类似LocalSend之类的传入请求即可，大多数时候拒绝入站能根本上解决一些公共网络中的安全问题。

### Java环境

- [Java - Arch Linux 中文维基](https://wiki.archlinuxcn.org/wiki/Java)

ArchLinux的Java充分考虑了Java生态碎片化的情况，准备了 java-runtime-common和java-environment-common包作为系统的Java环境管理工具，使得开发者可以在命令行切换目前使用的Java版本。

我们先安装一个21版本的OpenJDK

```
paru -S jdk21-openjdk
```

此时可以看到java-runtime-common也被作为依赖安装到了设备上，我们用命令查看一下目前支持的java环境：

```
# archlinux-java status

Available Java environments:
  java-21-openjdk (default)

```

可以看到我们所有的java环境和目前启动Java程序所使用的默认Java路径，后续我们只需要用：

```
archlinux-java set <JAVA_ENV_NAME>
```

就可以切换目前使用的Java环境了。

### intellij-idea系IDE的安装

我们可以在AUR里安装几乎全部的jetbrain公司的产品，但是安装后直接点击桌面图标是无法启动的（演示以webstorm为例）：

```
paru -S webstorm
```

此时**在电脑中已有Java环境的情况下**前往安装目录寻找启动脚本(以webstorm为例，目录应该在 /opt/webstorm/bin/webstorm.sh)并执行。该脚本可以在没有jetbarin的jre的情况下用默认Java环境打开对应IDE。

完成初始化后，我们在主菜单使用``Ctrl + Shift + A``快捷键搜索``选择IDE的启动Java运行时``（中文）或者``Choose Boot Java Runtime for the IDE``（英文），在新建里面随便下载一个（首次安装）/选择上一个IDE已经下载好的路径即可。

![选择Java运行环境](https://img.takuron.com/20250827/b3e0b010d074154b6e4695439b86b3b0.webp)

### 其他常用软件

- 视频：Vlc播放器 ``paru -S vlc vlc-plugins-all``
- 图形化文本编辑器：Visual Studio Code ``paru -S code``
- 办公三件套：LibreOffice ``paru -S libreoffice-fresh libreoffice-fresh-zh-cn`` 或者 ``paru -S libreoffice-still libreoffice-still-zh-cn``
- 录屏/直播：OBS ``paru -S  obs-studio``
