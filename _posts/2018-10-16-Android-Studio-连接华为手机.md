---
layout: post
title: Android Studio 连接华为手机
tags: [Android Studio, 华为手机]
excerpt_separator: <!--more-->
---
因夜神模拟器的诸多限制（卡而且应用不兼容），于是乎连接自己的华为手机
<!--more-->
### Android Studio 安装 Google USB Driver
> 参考：[2.Android Studio 无法连接华为手机的问题](https://blog.csdn.net/hang_jay/article/details/79372371)

### 安装华为手机助手 HiSuite
> 参考：[华为手机如何连接电脑进行安卓真机开发](https://blog.csdn.net/jmsw4bn/article/details/78653020)

### 使用华为手机助手 HiSuite 连接手机
- 根据软件提示连接手机，中途会提示打开 USB 调试，允许 HiSuite 设备连接等提示，尽可能勾选“默认此设备”，否则等到第二次连接的时候弹不出提示会连接不上。
- 如果连接不上按照软件提示来即可（废话。
- 如果还连接不上（1）查看USB调试是否打开（2）查看是否允许 HiSuite 设备连接
- 如果想取消默认开发者选项中有取消授权的选项
- 在我这里必须是华为手机助手连接成功，Android Studio 才能显示出 USB Device

### 出现 device unauthorized 的情况
- 重启 adb 服务即可
> 参考：[【stackoverflow】Android Studio - ADB Error - “…device unauthorized. Please check the confirmation dialog on your device.”](https://stackoverflow.com/questions/26079838/android-studio-adb-error-device-unauthorized-please-check-the-confirmat)