---
layout: post
title: ffmpeg+EasyDarwin 流媒体服务器
tags: [基础]
excerpt_separator: <!--more-->
---

转发USB摄像头的视频流，实现简单快捷

<!--more-->

### 下载

- [【Github】EasyDarwin 主页]([https://github.com/EasyDarwin/EasyDarwin#%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2](https://github.com/EasyDarwin/EasyDarwin#安装部署))

- [FFmpeg 下载](https://ffmpeg.zeranoe.com/builds/)

  >  选择 Shared 版本

- 播放器：VLC、Potplayer 等等都可以

### 使用

Step1：解压下载的 FFmpeg 压缩包，进入其 bin 文件夹下，在此目录下打开命令行（或者把这个目录加入至环境变量也可以）

Step2：命令行输入以下命令，查找USB摄像头设备名称

```c
ffmpeg -list_devices true -f dshow -i dummy
```

> 如果发现乱码则通过计算机管理-设备管理器查看设备名称

Step3：输入以下命令

```c
ffmpeg.exe -f dshow -i video="YOUR_CAMERA_NAME":audio="YOUR_MIC_NAME" -vcodec libx264 -acodec copy -rtsp_transport tcp -f rtsp rtsp://SERVER_IP/PATH
```

> 这个时候其实就可以打开 VLC、Potplayer 等播放器输入 `rtsp://SERVER_IP/PATH` 这个地址可以实时观看视频流了，EasyDarwin 实际上是将流转发并起到监控等控制的作用

Step4：解压下载的 EasyDarwin 压缩包，运行 EasyDarwin.exe，浏览器输入 [http://localhost:10008](http://localhost:10008/) 即可进入后台监控，查看推流、拉流列表

### 参考

- [**【EasyDarwin博客】ffmpeg 推送，EasyDarwin 转发，vlc 播放 实现整个 RTSP 直播**](https://www.easydarwin.org/article/EasyDarwin/30.html)

  > [**EasyDarwin系列博客**](https://blog.csdn.net/jyt0551?t=1)

- [**流媒体技术学习笔记之（十四）FFmpeg 进行笔记本摄像头 + 麦克风实现流媒体直播服务**](https://www.cnblogs.com/tinywan/p/6337504.html)

  > [**流媒体技术笔记系列**]([https://www.cnblogs.com/tinywan/tag/%E6%B5%81%E5%AA%92%E4%BD%93/default.html?page=2](https://www.cnblogs.com/tinywan/tag/流媒体/default.html?page=2)) 可以学习

- ffmpeg 命令行参数
  - [ffmpeg 命令行参数中文详解](http://www.mikewootc.com/wiki/sw_develop/multimedia/ffmpeg_app_param.html)
  - [FFmpeg 命令行](https://wklchris.github.io/FFmpeg.html)
  - [ffmpeg 参数中文详细解释](https://blog.csdn.net/leixiaohua1020/article/details/12751349)

