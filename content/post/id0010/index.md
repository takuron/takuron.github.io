---
title: cloudflare对gitlab page的加速效果
description: 实测Cloudflare免费CDN加速效果，对比加速前后差异，发现统计数据与直观体验有出入。测试显示对文本加速有效，但对图片等媒体文件却可能导致减速。结果喜忧参半。
slug: id0010
date: 2018-07-06 00:00:00+0000
image: https://i.loli.net/2020/02/07/mP7ndhTLWK4OzJX.jpg
categories:
  - Web
tags:
  - Web
  - CDN
  - Cloudflare
  - 测速
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

最近了解到了国外的免费cdn加速商cloudflare，自带https比那个要备案还非要收钱才能用https的百度加速云看上去好不少，所以测试了一下加速效果。

以下是本人新建的测试网页 **（已失效）**，二者网页内容完全相同：

[[gitlab无加速](https://speedtest-gitlab.zsxh.me/)](https://speedtest-gitlab.zsxh.me/)

[[cloudflare加速后](https://speedtest-cloudflare.zsxh.me/)](https://speedtest-cloudflare.zsxh.me/)

首先直观感受：无变化，甚至还能慢一点，尤其是图片加载的时候（这貌似在下面的文件下载测试时获得了证实），但是统计网站给的数据却让人大跌眼镜。

![6897991-93bb44ab03ebf065.jpg](https://i.loli.net/2020/03/26/faMh5uvgPX7Sm1x.jpg)

![6897991-da6128f02fa2482a.jpg](https://i.loli.net/2020/03/26/eTKQSd9ukmFIMCf.jpg)

前面的是没有经过加速的网页，很明显加载速度完全不是一个量级，后者秒杀前者，这和我们的直观体验不服，所以我换了一个更真实的网页测速器，[[webpagetest](https://www.webpagetest.org/)](https://takuron.top/go?url=https://www.webpagetest.org/)是一个利用真实浏览器来测试整个网页加载过程的测试网站，我们用它的北京节点来测试一下。

![6897991-fe680699c8973246.jpg](https://i.loli.net/2020/03/26/TRJfNXGw6MuFZrk.jpg)

![6897991-f24320387b2c93fc.jpg](https://i.loli.net/2020/03/26/cDnTYQsWBu95NgK.jpg)

很明显这个才和实际体验相符，cloudglare对文本文件进行了有效的加速，可对于图片等资源文件却一点不留情面，不仅没能加速反而搞起了减速。

文件下载测试可以选择网站里最上方的download，会出现一个视屏文件，利用那个视频文件可以测试下载速度。

![6897991-b804052a034d1c9b.jpg](https://i.loli.net/2020/03/26/w7y1Uhrt5kDoe4L.jpg)

看样子不仅是没有起到加速的作用，反而出现了减速的迹象，果然没有对媒体文件进行缓存。

综上，cloudflare对于页面的加速还是不错的，可惜由于抠门，媒体资源文件走cdn的减速抵消了页面的加速效果，所以说使不使用cloudflare完全是一个可选项，因为就算加速效果也不明显。
