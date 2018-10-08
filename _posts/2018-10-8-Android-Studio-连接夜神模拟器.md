---
layout: post
title: Android Studio 连接夜神模拟器
tags: [Android Studio, 夜神模拟器]
excerpt_separator: <!--more-->
---
(*^▽^*)
<!--more-->
### 将夜神模拟器bin文件夹路径添加至环境变量
- 在系统的环境变量 path 中添加 bin 文件夹路径，例如：你的夜神模拟器安装路径\Nox\bin;（我这里是D:\Software\AndroidSimulator\Nox\bin;）

### 验证 Android SDK 与 夜神模拟器 adb 版本是否一致
- 在命令行输入 adb version 和 nox_adb version（nox_adb 在 bin 文件夹下，如果找不到查看是否拼写错误）查看版本号是否一致
- 如果不一致，将 AndroidSDK 下 platform-tools 文件夹中的 adb.exe 复制一份，重命名并复制到夜神模拟器 bin 文件夹下（将原先的nox_adb.exe覆盖）

### 查找夜神模拟器端口
- 打开夜神模拟器，稳定后在命令行输入 netstat -ano |findstr 620，目的是查找夜神模拟器端口。我这里是 62025 与网上说的的 62001 不一致，因此需要手动查找。
> [【简书】轻松解决夜神逍遥模拟器模拟器等模拟器无法连接问题](https://www.jianshu.com/p/7642ab0c5afb)

### 连接模拟器
- 命令行执行命令：nox_adb connect 127.0.0.1:查找到的端口，例如我这里是 nox_adb connect 127.0.0.1:62025