---
title: 利用 Xposed 快速实现一个简易微信机器人
tags: [Xposed,微信]
date: 2018-05-09 13:26:00
categories: Xposed
---
本文同步至：https://www.jianshu.com/p/8d0d0b52bec6
# 目标
当前微信网页版限制越来越多，考虑尝试在手机上实现类似机器人的功能。本文目的是利用 Xposed 快速实现简易机器人功能，包括获取好友发来的消息，以及回复消息。后续可以增加智能回复，比如接入图灵机器人，或者自己自定义实现一些功能。
# 快速实现
## 项目框架的搭建
### WechatSpellbook - 站在"巨人"的肩膀上
[WechatSpellbook](https://github.com/Gh0u1L5/WechatSpellbook/) 是微信巫师作者在微信巫师的基础提取出来的通用微信 Xposed 插件框架。它提供了友好的的 API，提供自动分析微信内部结构特征的API(忽略微信版本差异)，对 hook 微信出现的常见问题都做了优化，总之就是使用它会更容易对微信 hook，感谢作者的贡献，项目的集成和详细介绍参见[wiki](https://github.com/Gh0u1L5/WechatSpellbook/wiki/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B)，以下步骤的实现都是基于这个框架的。
以下源码均基于微信 6.6.6 版本，由于使用了 WechatSpellbook 框架动态匹配的原理，大部分微信版本均可自动适配。
### 获得好友发来的消息
实现机器人功能的首要步骤就是获得好友发来的消息，获得消息之后才能回复吧，才能叫“机器人”吧。
使用了 WechatSpellbook，获取消息是很容易的，参见[api](https://github.com/Gh0u1L5/WechatSpellbook/blob/master/src/main/kotlin/com/gh0u1l5/wechatmagician/spellbook/interfaces/IMessageStorageHook.kt#L27)，当新消息存入数据库后回调，具体代码：
```kotlin
object WechatMessageHook : IMessageStorageHook {
    override fun onMessageStorageInserted(msgId: Long, msgObject: Any) {
        XposedBridge.log("onMessageStorageInserted msgId=$msgId,msgObject=$msgObject")
        // 这些都是消息的属性，内容，发送人，类型等
        val field_content = XposedHelpers.getObjectField(msgObject, "field_content") as String?
        val field_talker = XposedHelpers.getObjectField(msgObject, "field_talker") as String?
        val field_type = (XposedHelpers.getObjectField(msgObject, "field_type") as Int).toInt()
        val field_isSend = (XposedHelpers.getObjectField(msgObject, "field_isSend") as Int).toInt()
        XposedBridge.log("field_content=$field_content,field_talker=$field_talker," +
                "field_type=$field_type,field_isSend=$field_isSend")
        if (field_isSend == 1) {// 代表自己发出的，不处理
            return
        }
        // 做其他事情
    }
}
```
其中字段名含义如下：
- field_content： 消息内容
- field_talker： 发送者
- field_type： 消息类型
- field_isSend： 是谁发出的，我自己发出为1
这步到此就完成了，下一步是机器人怎么将消息回复给好友。

### 机器人回复消息
机器人回复消息需要找到发送消息出去这个 API，然后 hook 它，在我们的代码里调用就行了。

#### 利用 Monitor 的 Method Profiling 功能分析
首先在模拟器中打开微信聊天窗口，打开 Monitor，选中微信进程，点击`Start Method Profiling`，然后在聊天窗口随便发送一条消息，然后回来点击`Stop Method Profiling`，会生成分析文件。分析步骤如下：
1. 先搜索 click，点击发送按钮，肯定是触发了点击事件的嘛，先找找看
![image.png](https://upload-images.jianshu.io/upload_images/1340121-d6779724831e3dcc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 发现调用了 `ChatFooter$3.onClick()` 方法，单从名字上来看，应该就是这里了，点进去，看这个函数调用了哪里
![image.png](https://upload-images.jianshu.io/upload_images/1340121-02717958e085a261.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 它调用了 `chatting.o.FZ` 方法，注意参数是 String，返回值是 Boolean，大胆猜测一下，这个字符串就是消息文本，返回值应该是发送是否成功。验证一下，直接 Hook 这个函数，运行发现猜测是真的，这里比较简单就不贴代码了。
4. 分析到这里，已经知道了`chatting.o.FZ` 方法就是发送消息的，参数就是消息文本，但是有个很重要的地方忽略了，*为什么没有接收者参数？*，微信内部联系人 ID 一般是以 wx_idxxx 开头的，接收者 id 设置在哪，怎么设置 hook，现在就差这个问题了。
到这里已经知道了发送消息的 API，hook 掉就可以搞事情了，但是缺少接收者这个重要参数的设置，分析下源码吧。

#### 反编译查看源码分析
反编译之后分析 `chatting.o.FZ` 方法源码：
```java
  public final boolean FZ(String str) {
       mS(false);
       ctQ();
       return this.yOg.yRO.dt(str, 0);
   }
```
然后分析`yOg.yRO.dt`方法，它是`com.tencent.mm.ui.chatting.b`类的方法，看下源码：
```java
    public final boolean dt(String str, int i) {
        int i2 = 0;
        String Xf = bh.Xf(str);
        if (Xf == null || Xf.length() == 0) {
            w.e("MicroMsg.ChattingUI.TextImp", "doSendMessage null");
            return false;
        }
        x xVar = this.yXC;
        if (!ah.oB(Xf)) {
            az azVar = new az();
            azVar.setContent(Xf);
            azVar.eW(1);
            xVar.aB(azVar);
        }
        bt btVar = new bt();
        // 省略
    }
```
可以看到在`azVar.setContent(Xf);`这里将发送的消息文本放在放在了az这个类中，setContent() 是 az 的父类`com.tencent.mm.g.c.cg`的方法，看下这个类的源码：
```java
    // 截取了几个方法
    public final void av(long j) {
        this.field_createTime = j;
        this.eRw = true;
    }

    public final long wQ() {
        return this.field_createTime;
    }

    public final void ed(String str) {
        this.field_talker = str;
        this.feh = true;
    }

    public final String wR() {
        return this.field_talker;
    }

    public final void setContent(String str) {
        this.field_content = str;
        this.eRE = true;
    }
```
只截取了几个方法，可以看到这个类不仅仅包含消息文本，还包含了接受者`field_talker`，发送时间`field_createTime`等，大胆猜想，这个类就是消息的包装类，包含消息所有的属性，这里关注的字段是接收者 field_talker，只要知道在哪里调用了`ed`方法 hook 掉就可以为所欲为了。
但是，通过 AS 查找调用这个的地方有很多，根本无法判断具体发消息是哪里调用了，怎么办。
借助 Xposed 分析`com.tencent.mm.g.c.cg.ed()`方法，也就是设置接收者 field_talker 的方法，只要 hook 这个方法，然后打印出调用堆栈看看到底是哪里回调了。
```kotlin
        val clz = XposedHelpers.findClass("com.tencent.mm.g.c.cg", WechatGlobal.wxLoader)
        XposedHelpers.findAndHookMethod(clz, "ed", String::class.java, object : XC_MethodHook() {
            override fun beforeHookedMethod(param: MethodHookParam?) {
                log("set field_talker start")
                LogUtil.logStackTraces() // 打印调用堆栈
                log("set field_talker end")
            }
        })
```
打印结果：
![image.png](https://upload-images.jianshu.io/upload_images/1340121-24322dc99cd7282d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到函数调用链，关键点在`com.tencent.mm.modelmulti.i.<init>`，看下这个方法的源码：
```java
    public i(String str, String str2, int i, int i2, Object obj) {
        w.d("MicroMsg.NetSceneSendMsg", "dktext :%s", new Object[]{bh.cjG()});
        if (!bh.oB(str)) {
            cg azVar = new az();
            azVar.eV(1);
            azVar.ed(str);
            azVar.av(bd.in(str));
            azVar.eW(1);
            azVar.setContent(str2);
            azVar.setType(i);
            String a = a(((o) g.l(o.class)).s(azVar), obj, i2);
            if (!bh.oB(a)) {
                azVar.ej(a);
                w.d("MicroMsg.NetSceneSendMsg", "NetSceneSendMsg:MsgSource:%s", new Object[]{azVar.fnF});
        // 省略很多代码
    }
```
可以看到这个类的构造方法实例化了`cg azVar = new az();`，并调用了`ed()`方法。分析下这个构造函数，很有意思的是：参数 str 就是微信 id，str2是文本内容，后几个不知道，大胆猜测下这个类就是去发送消息的，从源码很难分析，hook 掉看看。
hook `com.tencent.mm.modelmulti.i`的构造方法打印参数，看下是否和发送消息有关。这里就不贴代码和截图了，结论是有关。那可以 hook 这个类的构造方法发送消息啊。

#### 找到的 hook 关键点
1. `com.tencent.mm.ui.chatting.o.FZ(String)` 方法，参数是消息文本，调用该方法可以发消息，但是无法设置接收者
2. `com.tencent.mm.modelmulti.i()`构造方法，第0个参数是接收者 id，第1个参数是消息文本

机器人回复消息思路：调用第一个 API 发送消息文本，hook 第二个 API 修改接收者 id，然后就可以愉快的发消息了

#### 关键点存在的问题
上述 hook 思路存在的问题：当 hook 第二个API 时，不知道该条消息的接收者是谁，不太好设置。

#### 问题解决方法
既然我能 hook 这两个 API，那么我可不可以直接在调用第一个 API 的时候，将接收者 id 放在文本消息前面，然后在 hook 第二个 API 时将文本消息中的接收者 id 解析出来赋值给第0个参数。
新消息文本 = 接收者ID + 分隔符号 + 真实消息文本
分割符号可以采用特殊字符，用户不会输入的字符，比如 \t 等

### 代码实现
[源码](https://github.com/Blankeer/WechatBotXposed)在这里，关键地方都有注释，有兴趣可以 star

### 效果图
![image.png](https://upload-images.jianshu.io/upload_images/1340121-3c4d1e968e8f7062.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
