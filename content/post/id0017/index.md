---
title: 记一次web工具制作：重制版与佛论禅
description: 从修复旧工具到重制经典！本文记录了如何基于“与佛论禅”理念，结合AES加密与生僻字映射，打造一款全新的网页版混淆/加密工具，并分享过程中的技术探索与心得。
slug: id0017
date: 2020-04-09 00:00:00+0000
image: https://i.loli.net/2020/04/09/C2HvEoy8lYzATPW.jpg
categories:
  - Web
tags:
  - Web
  - JavaScript
  - HTML
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

不知道该做点什么，突然有人向我反馈我以前写的加密应用不兼容Android10，想想觉得，干脆为大家重制一款用来加密的工具吧，毕竟老的也是我三小时作品。

为此我盯上了[与佛论禅](http://www.keyfc.net/bbs/tools/tudoucode.aspx)，这是一个很老的网页，里面是专门为老司机发链接而设计的一套混淆系统：利用一些生僻字来代指一些编码，从而达到混淆的目的。实现原理很简单，只要把某种混淆算法的结果的每一个字符对应一个生僻字就能实现。而且，不一定是混淆算法，同样的原理也可以用在加密算法上，脚踩两...一石二鸟，就这么愉快的开干了。

![Snipaste_2020-04-09_22-06-46.png](https://i.loli.net/2020/04/09/KREvGg6IrqzZuTh.png)

加密部分不用我们再造轮子，[crypto-js](https://github.com/brix/crypto-js)可以实现现在所有常见的加密算法，为此我可以直接选择了比较好用的AES加密算法，首先遇到的难题是：将AES加密的文本进行对应，作为著名懒狗，当然不会做那种一个个写对应文本的工作啊（况且我的语文水平还不够），然后，我在GitHub发现了宝藏：

![Snipaste_2020-04-09_22-32-55.png](https://i.loli.net/2020/04/09/QhjE5apUMmFeyNX.png)

感谢[这位老哥的对照表](https://github.com/Kwansy98/yufolunchan/blob/master/src/buddhism/TruthTable.java)，而且这位老哥的实现思路貌似和我一致，可惜是一个命令行的Java程序...

随后就是界面，虽然bootstrap过气了，4.0的栅格布局还默认为手机尺寸上手有点难受。但难看不代表难用，神奇的使用Dreamweaver完成了一次网页设计，反正来的快吗…

最后就是有意思的部分来了，作为一个混子，我还没学到加密算法原理的部分（讲了估计也不会认真听，纯理论这么枯燥我是难以静心学的），但在加密的生产实践中发现了规律：所有以ASE加密算法计算出的结果均以`U2FsdGVkX1`开头，很多网站都给出了类似说明，但均没有给予证明，**希望大佬可以给与证明或者指出错误**。不过为了佛语的随机性，一个开头相同的佛语很影响效率，所以本人砍掉了开头，目前为止，没有无法解密的文本。

另外，事后才知道ASE可以不需要密码。但当时为了统一性，本人为Web端设置了默认密码`TakuronDotTop`，这样在不需要加密仅需要混淆的情况下，可以直接将密码栏留空完成操作。

成品如下图所示，[国内版网页](https://takuron-tool.gitee.io/talk-with-buddha/)，[国际版网页](https://takuron-tool.github.io/talk-with-buddha/)，源代码在[github开源](https://github.com/takuron/talk-with-buddha)，基于MIT开源协议，欢迎Fork和Star。

![Snipaste_2020-04-09_22-52-50.png](https://i.loli.net/2020/04/09/u7J8ImLTUxdzjFP.png)