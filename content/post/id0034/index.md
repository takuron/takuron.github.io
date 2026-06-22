---
title: ArchLinux折腾记录02：使用问题合集
description: 记录ArchLinux使用过程中遇到的小问题和解决方案。
slug: id0034
date: 2026-06-22 00:00:00+0000
image: https://img.takuron.com/20250827/fd891f4ceba3bd23434784b7077c9044.webp
categories:
  - ArchLinux
tags:
  - Linux
  - ArchLinux
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

本系列用于聚合本人安装使用ArchLinux过程中遇到的一些问题，其中本篇用于记录一些不值得单独用一篇文章记录的一些小问题的解决方案，持续更新。

## Chromium在Wayland和Gnome环境下不显示关闭键

![fefe0c825dd4d52e9b93b9be45519ddb.webp](https://img.takuron.com/20251129/fefe0c825dd4d52e9b93b9be45519ddb.webp)

如图所示，有时Chromium在Wayland和Gnome环境下不显示三大金刚按键。最简单的解决方法是在设置里换成经典主题即可。

![a068870bec694d0d1c76b4a0f05d0aeb.webp](https://img.takuron.com/20251129/a068870bec694d0d1c76b4a0f05d0aeb.webp)

![e1f299c8d7834e1d44b948e689c5532b.webp](https://img.takuron.com/20251129/e1f299c8d7834e1d44b948e689c5532b.webp)

## 为Flatpak应用提供当前系统的字体设置

在终端中执行以下命令，无需指定具体的应用 ID，即可将权限应用到当前用户的所有 Flatpak 软件：

```bash
flatpak override --user --filesystem=xdg-config/fontconfig:ro
```

此命令会将用户目录下的 ~/.config/fontconfig 以只读（ro）模式挂载到所有 Flatpak 容器中。

如果需要撤销此全局设置，可以执行：

```bash
flatpak override --user --nofilesystem=xdg-config/fontconfig
```
