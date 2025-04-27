---
title: 安装Matrix系统（Riot.im）的服务端Synapse
description: 厌倦QQ/微信封号和审查？搭建自己的Matrix Synapse聊天服务器，实现通信自由与隐私！本文手把手教你如何在VPS上安装与配置Synapse，解决安装难题，创建专属安全聊天平台。
slug: id0015
date: 2020-02-09 00:00:00+0000
image: https://i.loli.net/2020/02/09/D9VJIRu8ioxEtls.png
categories:
  - 软件部署
tags:
  - 软件部署
  - VPS
  - Linux
  - 加密聊天
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

> 第四十条　中华人民共和国公民的通信自由和通信秘密受法律的保护。
>——《中华人民共和国宪法》

最近微信QQ封的越来越多了……真要说喜欢在网上胡说八道的封了就封了吧，我室友讨论了一晚上高数问题第二天QQ就被封了，这个老好人说他乱发表言论我都不信，更别说他自己都不知道为啥……所以一个真正自有可控的聊天软件已经成为了一个需求。无意中了解到了[Matrix](https://takuron.top/go?url=https://matrix.org/)系统，可以利用自有VPS安装服务端软件Synapse实现个人聊天服务器并通过多端的客户端软件连接，并且设置后可以以服务器作为代理连接所有Matrix服务器，反正已经有了一个网站服务器，就顺道玩玩吧。
可是这个安装真的是……一言难尽。在我的研究之下也没完全弄明白基于系统的安装方法，而基于pip的安装又各种问题……这里记录一下我基于系统软件安装的实现方法。

**本人才疏学浅，欢迎各位用过的大佬提出更好的方案，指正本人的错误。**

## 环境

Debian 9 ；已安装宝塔和Oneindex；在Root用户下进行操作。


## 安装Synapse

[Synapse](https://takuron.top/go?url=https://github.com/matrix-org/synapse/)是Matrix官方做的示例服务端，也是现在最推荐的客户端，这是官方的[安装文档](https://takuron.top/go?url=https://github.com/matrix-org/synapse/blob/master/INSTALL.md)，两种方法写的很凌乱……这里我以Debian为例写一下大概的步骤。

#### 1.安装Python环境

````
apt-get install build-essential python3-dev libffi-dev \
                     python3-pip python3-setuptools sqlite3 \
                     libssl-dev python3-virtualenv libjpeg-dev libxslt1-dev
````

#### 2.安装Synapse软件

根据官方推荐，这里添加了Matrix的安装源来安装最新版的Matrix。以下指令建议一个个录入来确保都能正确执行。

````
apt install -y lsb-release wget apt-transport-https
wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" |
    tee /etc/apt/sources.list.d/matrix-org.list
apt update
apt install matrix-synapse-py3
````

中间会提示以下两个界面，第一个问你服务器名称瞎写把，反正你不用这个服务器（下面会说），另一个问你是否愿意帮助他们，我们这么热心善良当然选否啊（滑稽）

![微信图片_20200209122736.png](https://i.loli.net/2020/02/09/95m7fPjerTa8Xis.png)

![微信图片_20200209122743.png](https://i.loli.net/2020/02/09/5k9nhGRHMVAyz8o.png)

安装好之后会自动运行服务器，如果是本机就可以访问`http://localhost:8008`来检查软件是否安装好了，vps吗……待会儿再说。

## 配置Synapse

我虽然知道这样安装之后会在`/etc/matrix-synapse/homeserver.yaml`生成配置文件，但我根本无法用官方文档里面的方法关闭这个服务器，更别提更新配置文件了。而且不更新配置文件的这玩意**不是不好用，是没法用**！所以我用了个技巧绕过了官方的服务器。如果谁知道这种方法安装的服务器怎么操作请务必告诉我。

#### 1.为新的服务器文件创建文件夹

这里可以按照自己喜好来操作

````
mkdir synapse
cd synapse
# /root/synapse/
````

#### 2.创建一套配置文件

````
/opt/venvs/matrix-synapse/bin/python -B -m synapse.app.homeserver -c homeserver.yaml --generate-config --server-name=takuron.top --report-stats=no
````

其中server-name按照官方要求是需要填写自己的域名的，这样才能正确的接入Matrix网络。不过如果是个人服务器的话其实可以随意写，第四个不知道是什么貌似也和接入Matrix网络有关，选no就好。

这会儿就可以启动服务器了，但在此之前需要一些配置。


#### 3.必须的配置

打开主配置文件`homeserver.yaml`，首先在207行：

````
#line 207-215
  - port: 8009
    tls: false
    type: http
    x_forwarded: true
    bind_addresses: ['::1', '127.0.0.1']

    resources:
      - names: [client, federation]
        compress: false
````

将端口改成8008以外的数，因为我们还没能成功关掉自带的服务器，这里曲线救国再建一个不同端口的服务器（反正默认都只能本地访问）

然后在862行：

````
enable_registration: true
````

开启注册（如果私有服务器，**回头记着关**，我是懒得一个个添加账号的，不如组织注册完后关掉注册）

然后在对应目录执行指令，启动服务器，我们的服务器就建立起来了。

````
synctl start
````

#### 4.可选的配置

![19125154.png](https://i.loli.net/2020/02/09/6xgOVHXv7aZIiw2.jpg)

## 配置反向代理

你会发现，上一步建立的服务器是没办法直接用ip+端口打开的，因为官方默认建立的就是本地服务器，虽然有直接把服务器暴露给网络的设置，但我们可以使用反向代理的方法，像网站一样管理服务器。这不是我的小聪明，这是官方推荐的做法，如果你没有宝塔，你可以按照[官方文档](https://takuron.top/go?url=https://github.com/matrix-org/synapse/blob/master/docs/reverse_proxy.md)来用普通的网站软件来解决问题，**记得把默认的8008端口改成你的端口**。这里我简单写写宝塔的配置方法。

新建一个网站，纯静态就可以，绑定好域名然后找网页设置-反向代理，按照底下图片的设置就好了，**记得修改端口为你自己的端口**。


![微信图片_20200209122759.png](https://i.loli.net/2020/02/09/rQy6hfd4KsTPIWp.png)

提交，看看外网访问是不是这个界面：

![390c7801fe180399277fbbbc22db44c6.png](https://i.loli.net/2020/02/09/KhWSLTrwGBlvMPd.png)

是的话大功告成。

## 客户端连接

**客户端足够多是我选择Matrix的重要原因**，这里有[可供连接的客户端的列表](https://takuron.top/go?url=https://matrix.org/clients/)，个人觉得其中[Riot.im](https://takuron.top/go?url=https://about.riot.im/)的客户端都已足够优秀并且支持足够多的平台，随便下载一个，将服务器设置为你的域名对应的网址（开TSL记得用https），然后就可以开始私有聊天了。

## 总结

>软件写得好，不如软件文档写得好。
>——鲁迅

软件文档引发的惨案……软件是好东西但文档真的人类迷惑搞得我转了半天在研究出这个不是方法的方法。不过有一说一，一整天解决了我和小伙伴的通信隐私问题，也算是目前情况下的一种收获吧。愿自由技术永远引导人类发展。
