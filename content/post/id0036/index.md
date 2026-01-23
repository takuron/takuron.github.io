---
title: 完美画质指南【1】：Windows平台Potplayer的基础配置
description: 本文是《完美画质指南》系列的首篇教程。针对 Potplayer 默认解码与渲染效果平庸的问题，详细讲解了如何利用 SVP4 安装包快速部署并正确配置 madVR 视频渲染器与 LAV Filters 分离/解码器。从视频渲染原理科普到手把手的设置步骤，帮助 Windows 用户以最低门槛解锁本地高清资源播放的画质潜力。
slug: id0036
date: 2026-01-23 00:00:00+0000
image: https://img.takuron.com/20260123/c405e52523482a813825d639c8b22613.webp
categories:
  - 视频技术
tags:
  - Potplayer
  - madVR
  - LAV Filters
  - 影音配置
  - 高清播放
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

> 原本这个方面我只是一个和其他人共同学习的小白，很多东西我也是更多参考别人。但我平时参考推荐给别人的配置教程改付费了把我气个半死，故为其补档供我以后参考教学。

随着近年来组建个人NAS的人越来越多，许多人又重新放弃了流媒体改为存储本地高清资源。但如何正确、高效地播放、存储、压缩这些资源又变得热门起来。Potplayer是一款使用很广泛的视频播放软件，但其默认的分离/解码器效果并不尽如人意，本篇文章将讲述如何正确地为Potplayer配置目前效果非常不错的视频渲染器madVR和滤镜分率器LAV Filters。

本文使用SVP4 Pro来快速安装正确配置的madVR和LAV Filters，如果有时间（那就是没时间）后续还会提供直接安装或者基于完美编码的整合安装教程更新在此。

## 视频文件如何变成显示像素

为了简单解释这些组件的必要性，这里我将简单讲解视频播放的过程，如果不愿意了解请直接跳转下一节。

视频播放是一个线性的流水线工作，数据会根据 **源文件 (Source) → 分离 (Splitter) → 解码 (Decoder) → 渲染 (Renderer) → 显示设备** 的顺序经过处理最终提供显示

视频文件（如 .mkv, .mp4）本质上是一个装载数据的容器（Container）。为了将封装在容器内混杂的“视频流”、“音频流”和“字幕流”拆分开来供后续环节处理，我们需要一个分离程序负责拆解这些“外壳”。LAV Filters 组件中的 LAV Splitter 正是负责这一关键的分离工作。

这之后分离出来的视频流（如 H.264/AVC, H.265/HEVC）和音频流（如AAC、FLAC）是经过高度压缩的二进制数据，计算机无法直接显示或者播放。为了让这些数据变成显卡可识别的原始图像数据， 包含在 LAV Filters 中的 LAV Video Decoder 和 LAV Audio Decoder 会进行这方面的解码工作。经过处理的数据会被还原为原始的图像数据（通常是 YUV 格式，如 NV12, P010）供下一步处理。

然后为了根据显卡厂商和显示器规格的不同将这些原始图像数据进行正确的显示（例如压缩原视频的10bit色深到你显示器的8bit、升高原视频1080P的分辨率到你的2K屏幕），madVR会对视频内容进行一系列包括但不限于色彩空间转换、色度升频、缩放、色调映射等操作，最终通过显卡发送显示数据给显示器完成视频大的播放。

## 安装过程

### 安装SVP4 Pro

> 这里我们只通过SVP4安装上述两个环境，不涉及补帧。因而只安装以上两个组件、至于补帧配置以至于SVP正版的购买和破解等均不涉及。

我们首先进行SVP的安装，在[SVP官网](https://www.svp-team.com/zh/get/)下载完最新版的SVP安装包后我们一路继续直到如下界面。

![3950af03822c8bc328ba3e9852936b77.webp](https://img.takuron.com/20260123/3950af03822c8bc328ba3e9852936b77.webp)

这里我们需要选择 Switch to components selection 来选择具体的组件安装，在展开后的菜单**取消勾选所有其他的组件**，只安装 madVR 和 LAV Filters 。

![c4f7ab4d8dbe7bdae310bbd6b6d8e1d4.webp](https://img.takuron.com/20260123/c4f7ab4d8dbe7bdae310bbd6b6d8e1d4.webp)

### 安装Potplayer

Potplayer的安装只需一路默认选项即可（或者可按个人偏好进行个性化自定义），唯一需要注意的是最后这个界面我们需要选择Detect H/W decoder/encoder 来检测硬件编解码器。另外OpenCodec建议不安装，自带的下载非常慢即使真有必要也请手动下载安装。

![ee461659c98c99175eb39bb4e690a3a8.webp](https://img.takuron.com/20260123/ee461659c98c99175eb39bb4e690a3a8.webp)

## 配置Potplayer

### 启用 madVR 和 LAV Filters

我们拉开Potplayer下拉选项，选择选项进入设置页面。首先选择视频，将视频渲染器选择为Madshi视频渲染即可，后续需要配置madVR直接选择这个选项后面的三个点即可进入配置页面。

![a48478172b5be9b5b15b70f0137c2516.webp](https://img.takuron.com/20260123/a48478172b5be9b5b15b70f0137c2516.webp)

然后我们进入滤镜-源滤镜/分离器，点击内置源滤镜/分离器设置。这里我们先选择添加系统滤镜，如上一步安装正确这里可以看到 LAV Filters 的四个重要组件，全部添加并且建议分别为其勾选所有可用的格式。

![ec73649108d4f3e32f5cde14892f7c06.webp](https://img.takuron.com/20260123/ec73649108d4f3e32f5cde14892f7c06.webp)

![85fee3fed4695348ce267901e1ec34c2.webp](https://img.takuron.com/20260123/85fee3fed4695348ce267901e1ec34c2.webp)

![b631627e3a2cfd62d269833a9cf62e16.webp](https://img.takuron.com/20260123/b631627e3a2cfd62d269833a9cf62e16.webp)

然后我们替换所有可以替换为 LAV Splitter Source 的源格式的源滤镜/分离器为 LAV Splitter Source ，若不能替换，保持默认选择或使用 Potplayer 内置的 FFmpeg 即可。 LAV Splitter Source 集成了文件读取和分离功能，作为源滤镜使用稳定性更好；而 LAV Splitter 仅负责分离，需要依赖其他滤镜读取文件。因此首选 Source 版本。

> 这里有个小技巧是直接滚轮一直往下滚就好了，一般最后一个选项都是 LAV Splitter Source ，滚到了移出去还能同时往下翻页。

![52af778dfffe2351350ffd51a974cc91.webp](https://img.takuron.com/20260123/52af778dfffe2351350ffd51a974cc91.webp)

接下来对于视频解码器/音频解码器进行类似的操作来设置LAV Video Decoder 和 LAV Audio Decoder。

![9e1211882015adaeb98e39af03276b3c.webp](https://img.takuron.com/20260123/9e1211882015adaeb98e39af03276b3c.webp)

![03a8d7faa10c359cd89981c10d420d75.webp](https://img.takuron.com/20260123/03a8d7faa10c359cd89981c10d420d75.webp)

### 其他可选选项

选择播放选项，打开鼠标指向进度条时显示缩略图。

![08923eaaaec1ba5bc5641beb6a64643f.webp](https://img.takuron.com/20260123/08923eaaaec1ba5bc5641beb6a64643f.webp)

## 验证配置正确

随便打开一个视频，按下Tab显示详细信息。正确的情况下这里可以看到视频解码器、音频解码器和视频渲染器如图所示即为已经正确安装了。

![79e6fcd76d422ff1502f843df1430687.webp](https://img.takuron.com/20260123/79e6fcd76d422ff1502f843df1430687.webp)
