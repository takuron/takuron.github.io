---
title: Android 13中使用Intent传输Parcelable对象为空的解决方式
description: Android13系统对API 33中新方法getParcelable方法的处理有问题，在使用Gradle 8.0以上版本进行混淆后会导致对象无法处理，这是一个bug，可以使用BundleCompat.getParcelable方法替代来解决问题。
slug: id0025
date: 2023-11-23 00:00:00+0000
image: https://img.takuron.com/20250827/1e7bc2d1c52123042ccb528e7263e7ca.webp
categories:
  - Android开发
tags:
  - Android
  - Kotlin
  - Android开发
  - Parcelable
weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## 背景介绍

### Intent对象传递与序列化

为了在Android各大组件之间通信跳转，Intent是最常用的通信手段。其中我们可以在其中的extra里面添加一些简单数据随其传递到下一个组件。为了向其中写入对象数据，我们需要将对象转变成可以保存和传输的二进制格式，即序列化过程。Android Intent允许传递继承Serializable和Parcelable的可序列化对象。
Serializable为Java原生的可序列化接口，只需继承自空的Serializable接口即可实现整个对象的序列化，但其序列化的实现使用了Java的反射机制且会对整个类的全部数据进行序列化，效率非常低。Parcelable则是Android专门设计的序列化接口，其需要自行实现序列化和反序列化的方法，其优点自然是[整体效率可以接近十倍于Serializable](go?url=https://www.developerphil.com/parcelable-vs-serializable/)，缺点自然是实现的时候需要写大量格式化的代码，以下是java中的一个例子：

````
static class Skill implements Parcelable {
        String name;
        boolean programmingRelated;

        Skill(Parcel in) {
            this.name = in.readString();
            this.programmingRelated = (in.readInt() == 1);
        }

        @Override
        void writeToParcel(Parcel dest, int flags) {
            dest.writeString(name);
            dest.writeInt(programmingRelated ? 1 : 0);
        }

        static final Parcelable.Creator<Skill> CREATOR
            = new Parcelable.Creator<Skill>() {

            Skill createFromParcel(Parcel in) {
                return new Skill(in);
            }

            Skill[] newArray(int size) {
                return new Skill[size];
            }
        };

        @Override
        int describeContents() {
            return 0;
        }
    }
````

### Kotlin的优化

Kotlin原生引入了data class，并且得益于gradle强大的插件系统。这解决了上面Parcelable接口最大的缺点，我们可以用插件快速为开发中常用的data类实现Parcelable序列化方法，首先在build.gradle里引入插件：

````
plugins {
    id("kotlin-parcelize")
}"
````

然后对需要实现Parcelable接口的数据类使用注释即可：

````
@Keep
@Parcelize
data class PaoModel(
    val pao_name: String,
    val pao_url:String
): Parcelable
````

### API33的改动

Api33以前无论读取那种序列化对象都是直接根据key来获得对象后强转：

````
val a:A = activity.intent.extras?.getParcelable("key1")
val b:B = activity.intent.extras?.getSerializable("key2")
````

但这样是类型不安全的，为了解决这个问题，Api33以后弃用了上述的方法，改为：

````
val a = activity.intent.extras?.getParcelable("key1",A::class.java)
val b = activity.intent.extras?.getSerializable("key2",B::class.java)
````

## 问题现象

在实际开发中我使用了Activity A B C，A B两个Activity为唯一跳转Activity C的路径并向其传递了需要展示的对象信息x，接收intent的数据的代码如下：

````
if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.TIRAMISU)
            historyData = ac.intent.extras!!getParcelable(AppInfo.INTENT_KEY_RENEYE,LgroModel::class.java)!!
        else
            historyData = ac.intent.extras!!.getParcelable<LgroModel>(AppInfo.INTENT_KEY_RENEYE)!!
````

在测试的时候未发现任何问题，于是混淆打包release，出事了，提示空指针。

首先考虑混淆导致data类错误和强制非空转换的逻辑问题，但查了半天该keep的都正常keep，逻辑在debug包下无论怎么点都不会有问题。尝试打印传入对象，在release包下依然有效。尝试传递字符串，传递正常。于是问题就变成了为何intent传递对象失效。

## 解决

问题回到了获取Parcelable对象本身，此时看到为了适配新api做的分支判断，马上掏了个Android 11的手机测试，一切正常，问题看样子出在了新的方法上，遂根据关键词搜索，找到了[Stack Overflow上的这篇文章](go?url=https://stackoverflow.com/questions/76067109/getparcelable-crashes-due-to-null-iftable-in-agp-8),真相大白，解决方案翻译如下：

> 我认为这个崩溃是由两个不幸的因素组合在一起引起的。
> AGP 8 默认启用了 R8 的完整模式。R8 的完整模式会剥离默认构造函数并执行更激进的优化。在 API 33 中引入的新的 getParcelable(String, Class) 方法存在一些缺陷，如果 Parcelable 没有以特定方式定义，就会出现问题。[相关错误的文档](go?url=https://issuetracker.google.com/issues/274185314)
> 看起来 getParcelable(String, Class) 的脆弱实现和 R8 对某些代码进行优化导致了这个崩溃。目前的解决方法似乎是在 API 33 中使用旧的 getParcelable(String) 方法。Google 表示他们已经在 API 34 中修复了 getParcelable(String, Class) 的问题。

> 更新：androidx.core:core-ktx:1.10.0 包含了对此问题的修复。它包含了 BundleCompat.getParcelable(Bundle, String, Class)，该方法仅在 Android U 及以上版本上调用新的 getParcelable(Bundle, Class) 函数。

这个问题只会在使用R8混淆之后才会出现且较难定位。所以为了解决这个问题，现在建议升级AndroidX之后使用其中的方法来获取Parcelable对象。故修改：

````
if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.TIRAMISU)
            historyData = BundleCompat.getParcelable(ac.intent.extras!!,AppInfo.INTENT_KEY_RENEYE,LgrolrModel::class.java)!!
        else
            historyData = ac.intent.extras!!.getParcelable<LgrolrModel>(AppInfo.INTENT_KEY_RENEYE)!!
````

（后来发现用了兼容库版本判断也是多余的）

````
historyData = BundleCompat.getParcelable(ac.intent.extras!!,AppInfo.INTENT_KEY_RENEYE,LgrolrModel::class.java)!!
````

问题解决