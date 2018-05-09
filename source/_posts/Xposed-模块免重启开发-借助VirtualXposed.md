---
title: Xposed 模块免重启开发(借助VirtualXposed)
tags: [Xposed,VirtualXposed]
date: 2018-05-09 12:31:05
categories: Xposed
---
本文同步至：https://www.jianshu.com/p/938e8c4c00df
# Xposed 模块开发痛点
Xposed 模块修改之后是需要重启手机生效的，导致开发非常麻烦，改个代码运行还要重启，等待时间太长。
# 现有的方案
搜索了一下，有现成的方案，原理大多是修改 Xposed FrameWork 源码实现，还有一种思路是动态加载。但都有点麻烦，还存在一些问题，偶尔失效只能重启。
参考：
https://github.com/shuihuadx/XposedHook
https://www.jianshu.com/p/d5596196bd12
https://bbs.pediy.com/thread-223713.htm
http://androidwing.net/index.php/274
# VirtualXposed 方案
[VirtualXposed](https://github.com/android-hacker/VirtualXposed) 主要功能是在非ROOT环境下运行Xposed模块。使用之后觉得它比较适合模块开发，原因几下几点：
1. 支持免重启手机激活模块
2. 对开发者友好，详见 [wiki](https://github.com/android-hacker/VirtualXposed/wiki/Utilities-For-Xposed-Module-Developer)
3. 项目开源，作者很活跃，遇到什么问题很快可以得到答复

但是还是有一些缺点的：
1. 不支持 x86，也就是不支持模拟器，只能使用真机
2. 暂不支持资源HOOK
3. 部分插件的兼容性有问题
4. 不能 hook 系统 API
5. 使用必须将需要 hook 的 APP 和模块 APP 安装到VirtualXposed

如果以上缺点提到的有涉及的就不能使用该方案

## Gradle Task 实现自动重启 VirtualXposed，自动更新模块
以下配置环境是 Android Studio
[wiki](https://github.com/android-hacker/VirtualXposed/wiki/Utilities-For-Xposed-Module-Developer) 里提供重启 VirtualXposed 、自动更新 APP，打开某 APP 的广播方式。利用这些可以编写Gradle Task 实现运行项目自动更新模块 APP，自动重启VirtualXposed，自动打开需要 hook 的 APP。
需要先将需要 hook 的 APP 和模块先安装到 VirtualXposed，再进行以下设置：
1. 将 Debug Configurations 里将 Gradle aware Make - 修改为 :app:installDebug
![image.png](https://upload-images.jianshu.io/upload_images/1340121-bd38cde2cf2897b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 修改 app/build.gradle 文件，在最下面增加如下代码
```gradle
afterEvaluate {
    installDebug.doLast {
        updateVirtualXposedAPP.execute()
        rebootVirtualXposedAPP.execute()
        launchVirtualXposedAPP.execute()
    }
}
// 更新 VXP 中的 app
task updateVirtualXposedAPP(type: Exec) {
    def pkg = android.defaultConfig.applicationId
    commandLine android.adbExecutable, 'shell', 'am', 'broadcast', '-a', 'io.va.exposed.CMD', '-e', 'cmd', 'update', '-e', 'pkg', pkg
}
// 重启 VXP
task rebootVirtualXposedAPP(type: Exec) {
    commandLine android.adbExecutable, 'shell', 'am', 'broadcast', '-a', 'io.va.exposed.CMD', '-e', 'cmd', 'reboot'
}
// 重启 VXP 需要 hook 的 APP，需要知道它的包名
task launchVirtualXposedAPP(type: Exec) {
    def pkg = 'com.tencent.mm'// 需要 hook 的 app，这里是微信
    commandLine android.adbExecutable, 'shell', 'am', 'broadcast', '-a', 'io.va.exposed.CMD', '-e', 'cmd', 'launch', '-e', 'pkg', pkg
}
```
具体代码参见：[MDWechat](https://github.com/Blankeer/MDWechat/blob/master/app/build.gradle#L45)
原理就是利用 Gradle Task 使用 adb  发送广播。
以上配置好就可以愉快的敲代码了。
