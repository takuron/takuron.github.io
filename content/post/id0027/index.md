---
title: OPNsense使用记录01：安装与基本配置
description: 本文记录了OPNsense系统的详细安装与基本配置过程，包括硬件选择、系统安装步骤、多LAN口桥接设置、基础防火墙规则配置、WAN口PPPoE拨号、IPv6初步启用（SLAAC）、DHCPv4设置以及系统更新和配置备份，为新用户提供了实操指南。
slug: id0027
date: 2024-11-16 00:00:00+0000
image: https://img.takuron.com/blogpost/KABjvaXbhSmWkCg.webp
categories:
  - OPNsense
tags:
  - 网络
  - 路由器
  - 组网
  - OPNsense
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

自从去年受够openwrt各种插件之间的兼容性问题和遭遇了n5105小包总线速度问题导致的系统崩溃之后转向了OPNsense系统。作为一个专业的开源防火墙，其中文资料却少得可怜，去年第一次装的时候就准备全程记录一下配置的过程，一直因为时间问题没能实现。到了现在软路由硬件都换了一遍，结果再装还是资料少的可怜频频踩坑，故开坑记录一下，免得我自己也天天往一个坑里跳。

## 前置条件

能选择这个系统的应该对软路由硬件挑选已经有自己的见解了吧，OPNsense 是基于 FreeBSD 的开源防火墙系统，理论上有驱动的硬件你随便选就可以。这里本人使用的是畅网n100的四网畅想版ddr4的版本来安装，并不是什么高性价比的东西单纯偷懒。内存为8g，硬盘则是用了一个傲腾16g，反正不记录log只装系统我就没见过用爆的，本身量大寿命又长，除了强制要有m.2口外其实很适合做这种工控机的系统盘。

![使用的软路由](https://s2.loli.net/2024/11/16/76vmM8Q5gTxUqFA.jpg)

[这里](go?url=https://opnsense.org/users/get-started/)可以查到官方的硬件要求。

[你可以在这里下载到OPNsense](go?url=https://opnsense.org/download/)，下载类型选择amd64和dvd，镜像国内选北京大学的即可。下载好后可以直接用[rufus](go?url=https://rufus.ie/zh/)写入u盘安装。

![下载界面](https://s2.loli.net/2024/11/16/W9buyensYvR73xd.webp)

## 系统安装

> 因本人没有采集卡并且不喜欢拍屏，故本节非web界面的截图均来自[b站@狐狸Nomad](go?url=https://www.bilibili.com/opus/784755568172597299)，顺便感谢大佬的指南。

从u盘启动，一直跑马直到出现“Press any key to start the configuration importer: ...”。不用管。

![mflzo9aaoffx550e.webp](https://s2.loli.net/2024/11/16/E2uJVXs6jCLgGdA.webp)

等到跑马出现“Press any key to start the manual interface assignment”的时候，你可以点击任何键来配置接口，在这里你可以手动配置一下wan口和lan口。但由于OPNsense的逻辑和openwrt不太一样这里的lan口只能配置一个，我倾向于直接跳过回头webui里面配置。如果你需要改可以按提示设置一下lan和wan对应的网口，其他选项除了最后保存全都直接回车跳过即可。

![yioc701c2ndx4hpy.webp](https://s2.loli.net/2024/11/16/lGhiKkZEBuw7x3a.webp)

![x3xnsvza5ao3sy2d.webp](https://s2.loli.net/2024/11/16/cD1TMyPBCOrw26i.webp)

然后就提示登录了，这里我们可以看到OPNsense为我们启动了一个体验版的系统，你可以连接lan口来体验。这里我们登录安装账号来开始安装到本地，在24.7版本安装账号名为installer,密码opnsense。

![t50aqha0g8eufwp4.webp](https://s2.loli.net/2024/11/16/mN2i3JsAHxQ7gGV.webp)

登录后就进入了安装界面，选择默认键盘布局。

![cuxvjptvwch2gfi9.webp](https://s2.loli.net/2024/11/16/WcLKmDgY3e5hJqH.webp)

选择安装的硬盘分区格式，现在默认就是ZFS选择就可以了。

![067n3phbpqsa7inw.webp](https://s2.loli.net/2024/11/16/GBVRW27Z93Dvifm.webp)

选择冗余模式，你可以在这里给系统做软raid，这里一块盘选stripe。

![8656mlvm1u46q747.webp](https://s2.loli.net/2024/11/16/hOI1PvJxFLUZ4EX.webp)

选择安装的硬盘

![qaglrtzszpnerk7e.webp](https://s2.loli.net/2024/11/16/Ay863jMLWHT5rhJ.webp)

然后就开始安装了，安装结束后会显示这个界面，建议在这里修改一下root用户的密码，回头你需要用这个密码登录管理界面。

![zeo9w02o5pd2nhx8.webp](https://s2.loli.net/2024/11/16/BxSnqEpr5iHPWdh.webp)

重启，然后等待到提示登录时按照显示的lan口和网关地址打开浏览器就可以看到登录界面了（ssl证书错误请直接了解详细，仍要访问即可）。登录用户名为root，密码为你上一步设置的密码。

![Snipaste_2024-11-16_19-34-41-tuya.webp](https://s2.loli.net/2024/11/16/6Rt4Fim7SenPQTO.webp)

![Snipaste_2024-11-16_12-28-43-tuya.webp](https://s2.loli.net/2024/11/16/C9TbK4WuitFQS8g.webp)

然后就可以接上wan口开始配置了。

## 基本配置

### 初始化

初始化其实按照引导一步步设置即可，首先设置时间和时区，这里可以改成阿里的btp服务器也可以不动。

![Snipaste_2024-11-16_11-12-26-tuya.webp](https://s2.loli.net/2024/11/16/vBoS8WcAGPqf6Ht.webp)

设置主机名、dns和语言，这些我们都不用动后面会用AdGuardHome。

![PixPin_2024-01-17_14-33-06-tuya.webp](https://s2.loli.net/2024/11/16/MjPGNLkrOHzxsIW.webp)

这里有一个图少截了，会问你WAN口的类型，我这里还没插网线直接选的pppoe但没给账号密码，如果上一步你接上了网线可以这会儿就配置了。

设置LAN ip和网段，选一个自己喜欢的局域网网段即可。这里我写错了网关地址应为172.24.0.1。

![Snipaste_2024-11-16_11-16-28-tuya.webp](https://s2.loli.net/2024/11/16/IFhcfkrTKPJjgBG.webp)

然后机器就会重启，初始化就基本完成了。

### 启用所有LAN口

pfsense和OPNsense的第一个坑就是他们的接口（interface）都只能实际绑定一个物理网口，**如果需要给几个网口做默认桥接就要每个网口单独设置一个没有网络的接口，然后用网桥连接这些接口后绑定在LAN上**。这和openwrt的习惯是不一样的。

我们现在就来把我这个机器的igc1/2/3设置成互相桥接的LAN口，目前我已经给igc1在安装的时候设置成了机器的LAN，所以我们献给igc2/3建立一个网桥。先建立两个接口，分别绑定物理的网口2/3并启用，不需要任何ip设置。

![Snipaste_2024-11-16_22-53-51-tuya.webp](https://s2.loli.net/2024/11/16/4b9rzAoOsVP6nCF.webp)

然后我们建立网桥，在接口/其他类型/网桥里面点一下加号，将刚才新建的两个接口加进去。

![Snipaste_2024-11-16_12-48-16-tuya.webp](https://s2.loli.net/2024/11/16/vYslxmLFd1qeZ8U.webp)

进入我们原先的LAN接口，把LAN接口改成我们新建立的网桥，保存。（注意所有的接口配置都需要二次选择应用才能生效）

![PixPin_2024-01-17_14-59-40-tuya.webp](https://s2.loli.net/2024/11/16/xhrIaGD9LpWAokb.webp)

接入网口2/3，再将已经释放的igc1同样的创建接口并加入网桥即可。

### 防火墙初步

以上述方法设置的LAN口虽然都可以正常访问网络，而且我们实际做局域网DHCP和NAT的“LAN”接口的规则是进出全部放行，但由于我们引入了新的接口，而这些接口的默认规则是放行出口禁止进口，这样会导致局域网下设备无法互相访问。

我们点开防火墙/规则，随便选择一个lan口，加号新建规则，这里我用最简单的方法先放行了所有LAN网络下的主机的入站，这样可以把最需要解决的局域网互相访问先处理好，后续感觉应该修改掉LAN口的全放行来控制流量更符合家用的环境。记得保存完规则后刷新让防火墙生效。

![Snipaste_2024-11-16_23-14-52-tuya.webp](https://s2.loli.net/2024/11/16/fKWw8ersMQVEh17.webp)

![Snipaste_2024-11-16_23-14-15-tuya.webp](https://s2.loli.net/2024/11/16/qcztvDEerpwV5d7.webp)

### 拨号、DHCP和IPV6初步

我们转回到接口WAN，这里以山东联通普通家用有v6的环境为例进行拨号和通过上级路由分配ipv6地址。PPPoe设置非常的简单，只需要在这里写你宽带的用户名和密码就行。

![Snipaste_2024-11-16_12-51-39-tuya.webp](https://s2.loli.net/2024/11/16/5erXQj4PKCwhc9s.webp)

然后是ipv6，拖到最底下可以看到DHCPv6客户端的设置，这里比较重要的是使用ipv4连接和仅请求地址前缀（request prefix only）必须打开。前缀委派大小和发送前缀大小这俩开与不开意义不大反正会被运营商美美无视。

![Snipaste_2024-11-16_23-30-33-tuya.webp](https://s2.loli.net/2024/11/16/gRt9LsvwbciG1OM.webp)

在LAN口中打开允许手动调整DHCPv6和路由器通告。

![Snipaste_2024-11-16_23-31-12-tuya.webp](https://s2.loli.net/2024/11/16/LDvoBWAXs6bakFg.webp)

此时打开服务/DHCPv6，如果宽带能正常获取ipv6前缀应该可以看到分配的网段，长度从56bits到64bits不等，说明获取ipv6前缀成功。（不过先不要用DHCPv6，我们现在先用SLAAC分配v6更加方便快捷）

![Snipaste_2024-11-16_23-32-07-tuya.webp](https://s2.loli.net/2024/11/16/FdQajGlLuOsw7CY.webp)

我们打开服务/路由器通告，将通告模式设置成unmanaged仅使用SLAAC分配ipv6，这样最基本的拥有ipv6地址就做到了，后面我们会更详细的设置DHCPv6。

![Snipaste_2024-11-16_14-35-55-tuya.webp](https://s2.loli.net/2024/11/16/QcMNgoTUYEBjDFA.webp)

同一级菜单下还可以改变DHCPv4的设定，最需要设置的可能是类似我这种给了更大的局域网网段，这种情况下DHCP可选的ip范围还是不够使用的话可以手动选择范围。

### 更新 备份 杂项

转到系统/固件/设置，这里可以先把更新源改成国内的，然后保存返回状态进行更新。

![Snipaste_2024-11-16_15-20-12-tuya.webp](https://s2.loli.net/2024/11/17/CzRZNOS31quPULa.webp)

系统/设置/其他里面可以修改主页温度传感器的温度源，志强用户还可以开启硬件加速。

![Snipaste_2024-11-16_15-07-26-tuya.webp](https://s2.loli.net/2024/11/17/V7SiA9v82Ya4bQq.webp)

系统/设置/任务里面可以为系统设置定时任务，这里我让他每周重启。

![Snipaste_2024-11-17_09-43-23-tuya.webp](https://s2.loli.net/2024/11/17/OvhMsWmTDBRUPg1.webp)

最后我们可以吧已经配置好的配置文件下载下来，这样出现问题直接上传配置文件即可恢复全部设置了。

![Snipaste_2024-11-17_09-40-51-tuya.webp](https://s2.loli.net/2024/11/17/FpPfHieclrKymCo.webp)

结束

> 最后吐槽一下国内折腾软路由的，表面说支持开源和稳定，实际用着各种高大全魔改TUN的固件，为了开个魔法上网拒绝一切技术进步，ipv6这些和全局魔法冲突的统统全杀了还是避免不了不同模块打架出奇葩问题。你要和他说起稳定转头就投了有黑历史的闭源ikuai，就这样还热衷于all in boom建议两个全都真是系统简洁明了稳定。
>
> 我个人不喜欢纯粹的全局代理，因为没有一个固定的规则让我满意能完成我的一切需求，毕竟人的需求也是会根据实际情况变动的。个人还是更喜欢细化的给每个需要分流的软件单独设置代理，浏览器直接用两个，这种手动分类才是效率最高的。

