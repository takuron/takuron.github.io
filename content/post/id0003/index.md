---
title: Android studio 2.2.3默认项目状态栏层级错误的解决方案
description: 刚用Android Studio新建项目就遇到状态栏阴影Bug？这个恼人的视觉错误曾让我头疼数月！本文分享亲测有效的解决方案，帮你轻松去除状态栏阴影！
slug: id0003
date: 2017-10-15 00:00:00+0000
categories:
  - Android开发
tags:
  - Android开发
  - Android
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

最近在android studio里新建了一个项目，本来觉得as真的很人性化，给了这么多模板，还都带material design效果，可是当我编译完成后……

![597d814a7fd92.png](https://i.loli.net/2017/07/30/597d814a7fd92.png)

握草这是什么玩意，最简单的hello world都给我附赠个状态栏层级错误，强迫症表示上面的阴影真的是令人无法忍受啊。

![597d81cc78f57.gif](https://i.loli.net/2017/07/30/597d81cc78f57.gif)

然后找解决方案，国内是完全没有这方面资料（至少我没找到）（国人果然对md还是非常冷淡），英语水平又呵呵的很，所以一等就是好几个月没有理这个bug。

前几天和朋友聊起这个bug，他们建议我去stackoverflow 再找找。一开始我是拒绝的，因为我这访问sf奇慢（开网页分钟级），但当他替我搜到时，我就在感叹：国外人真会玩啊。

[stackoverflow原文地址](https://stackoverflow.com/questions/34891756/toolbars-shadow-on-status-bar-for-lollipop)

国外dalao提供了四种解决方案，下面我写一下我亲试有效的两种：

##### 1.去除statusBarColor

关于这个属性，android官方文档是这么解释的

> To set a custom color for the status bar, use the android:statusBarColor attribute when you extend the material theme. By default, android:statusBarColor inherits the value of android:colorPrimaryDark.

> 本人借助翻译软件的渣翻：要为状态栏设置自定义颜色，可以在扩展主题主题时使用android:statusBarColor属性。默认情况下,android:statusBarColor继承了android的值:colorPrimaryDark。

也就是说，我们现在需要它是透明的。然而透明是android的默认选项，虽然根据回答者的意思是我们在代码里就设置的是透明，但这完全没有必要，所以，我们应该找到value-v21里的style.xml，把这行：

```

<item name="android:statusBarColor">@android:color/transparent</item>

```

去掉就OK了。

##### 2.外面加一个LinearLayout

很玄的一个方法，然而的确是有效的，不做首要推荐。

![597d89a8f04cb.png](https://i.loli.net/2017/07/30/597d89a8f04cb.png)

（由于简书和我的博客老抽，只好先截图了，具体的可以到原文里查看）

以上两种方法处理之后界面就恢复正常了～

![597d8a0b730c7.png](https://i.loli.net/2017/07/30/597d8a0b730c7.png)
