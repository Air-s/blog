---
layout: post
title: FFmpeg+EasyDarwin 流媒体服务器
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

```bash
ffmpeg -list_devices true -f dshow -i dummy
```

> 如果发现乱码则通过计算机管理-设备管理器查看设备名称

命令行输入以下命令，查看USB摄像头流信息

```bash
ffmpeg -list_options true -f dshow -i video="YOUR_CAMERA_NAME"
```

Step3：查找554端口是否被占用

```bash
netstat -aon|findstr "554"
```

> 或者停止 Windows Media Player Network Sharing Service 服务

Step4：解压下载的 EasyDarwin 压缩包，运行 EasyDarwin.exe，浏览器输入 [http://localhost:10008](http://localhost:10008/) 即可进入后台监控，查看推流、拉流列表

> 如果不先打开服务直接运行 FFmpeg 可能会出现 real-time buffer [video input] too full or near too full 错误，关于这个问题讨论可以看 [这篇文章](https://forums.vmix.com/posts/t5692-Streaming-error-real-time-buffer--vMix-Video---video-input--too-full-or-near-too-full) 和 

Step5：输入以下命令

```bash
ffmpeg.exe -f dshow -i video="YOUR_CAMERA_NAME":audio="YOUR_MIC_NAME" -vcodec libx264 -acodec copy -rtsp_transport tcp -f rtsp rtsp://SERVER_IP/PATH
```

> ① 如果想停止流传输进入EasyDarwin后台界面停止它尽可能不要用Ctrl+Q退出 ② 这个时候其实就可以打开 VLC、Potplayer 等播放器输入 `rtsp://SERVER_IP/PATH` 这个地址可以实时观看视频流了，EasyDarwin 实际上是将流转发并起到监控等控制的作用

```bash
# 多线程计算
-threads 2
# 输入设备从directshow接口获取，并指定视频与音频设备名称
-f dshow -i video="YOUR_CAMERA_NAME":audio="YOUR_MIC_NAME"
# 使用x264压缩编码，编码速度指定中等，一定压缩比率压缩（范围是0-51），设置分辨率为720x576
# X264提供三种码率控制的方式：bitrate, qp, crf。这三种方式是互斥的，使用时设置其中之一即可。
# tune参数非必要不需设置
-vcodec libx264 -preset medium -tune zerolatency -crf 26 -s 720x576
# 设置其他参数（这里设置关键帧间隔、最小关键帧间隔，不过不建议设置这两个参数）
-x264-params keyint=123:min-keyint=20
# 拷贝原始音频流（也可采用 -acodec aac）
-acodec copy
# 指定rtsp传输方式，如果不指定默认为UDP
-rtsp_transport tcp
# 指定输出方式，也可设置为-f flv等格式
-f rtsp
```

> ① 输入设备与接口可参考[《FFmpeg官方文档》](https://ffmpeg.org/ffmpeg-devices.html) ② libx264 参数具体含义可参考 [此博客](https://www.cnblogs.com/poissonnotes/p/6904728.html) 和 [此文章](http://livevideostack.com/portal.php?mod=view&aid=22) ③ 如果想对 rtsp 更细致调整可参考 [此文章](https://weichao.io/2018/07/29/FFmpeg-录制-RTSP-流/)

Step6：多路输出（以下命令为 rtsp 推流和录制流输出至本地 mp4 文件）

```bash
ffmpeg.exe -threads 4 -thread_queue_size 1024 -rtbufsize 124M -flags low_delay -max_delay 0 -f dshow -i video="USB2.0 PC CAMERA":audio="麦克风 (USB Audio Device)" -vcodec libx265 -crf 26 -tune fastdecode -x264-params keyint=50 -acodec aac -rtsp_transport tcp -f rtsp rtsp://192.168.0.100/test -vcodec libx265 -crf 26 -acodec aac -f mp4 C:\Users\Dell\Desktop\text.mp4
```

> ① 多路输出方式格式为 -vcodec xx -acodecxx -f xx PATH1 -vocodec xx -acodec xx -f xx PATH2，当然也可以使用 -f tee 或 -f matroska 等方式多路输出 ② 关于延迟，使用 udp 传输比 tcp 启动快且延迟小，缺点是可能会出现花屏，延迟也可能与播放器有关，相关讨论 [Stack Overflow: how to minimize the delay](https://stackoverflow.com/questions/16658873/how-to-minimize-the-delay-in-a-live-streaming-with-ffmpeg) 和 [FFmpeg Wiki](https://trac.ffmpeg.org/wiki/StreamingGuide#Latency)

### 参考

- [**【EasyDarwin博客】ffmpeg 推送，EasyDarwin 转发，vlc 播放 实现整个 RTSP 直播**](https://www.easydarwin.org/article/EasyDarwin/30.html)

  > [**EasyDarwin系列博客**](https://blog.csdn.net/jyt0551?t=1)

- [**流媒体技术学习笔记之（十四）FFmpeg 进行笔记本摄像头 + 麦克风实现流媒体直播服务**](https://www.cnblogs.com/tinywan/p/6337504.html)

  > [**流媒体技术笔记系列**]([https://www.cnblogs.com/tinywan/tag/%E6%B5%81%E5%AA%92%E4%BD%93/default.html?page=2](https://www.cnblogs.com/tinywan/tag/流媒体/default.html?page=2)) 可以学习

- ffmpeg 命令行参数
  - [ffmpeg 命令行参数中文详解](http://www.mikewootc.com/wiki/sw_develop/multimedia/ffmpeg_app_param.html)
  - [FFmpeg 命令行](https://wklchris.github.io/FFmpeg.html)
  - [ffmpeg 参数中文详细解释](https://blog.csdn.net/leixiaohua1020/article/details/12751349)
  - [FFmpeg X264 的 preset 和 tune 参数](https://www.cnblogs.com/poissonnotes/p/6904728.html)

- FFmpeg 实战
  - [【简书】FFmpeg 推流总结](https://www.jianshu.com/p/37ef34258608)
  - [【简书】FFmpeg 常用推流命令](https://www.jianshu.com/p/d541b317f71c)
  - [【FFmpeg】 ffmpeg 常用命令](https://www.cnblogs.com/frost-yen/p/5848781.html)
  - [【xf's Blog】FFmpeg 流媒体使用总结](https://zxf.me/2019/06/20/FFmpeg-流媒体/)
  
- [**crf数值对文件大小的影响——用 ffmpeg 命令行转压视频**](https://segmentfault.com/a/1190000002502526)

- [**crf数值对文件大小的影响——FFmpeg 视频转码技巧之 - crf 参数（H.264 篇）**](https://blog.csdn.net/happydeer/article/details/52610060)

- [**preset值对延时与质量的影响——ffmpeg 的转码延时测试与设置优化**](https://blog.csdn.net/fireroll/article/details/51902018)

- [**音视频文件码率与大小计算**](https://www.cnblogs.com/kakafra/p/3326011.html)

  > 800kbps 来编码表示经过编码后的数据每秒钟需要用 800K **比特**来表示，比特（bit/b），1KB=8Kb

