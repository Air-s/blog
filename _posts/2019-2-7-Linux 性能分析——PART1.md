---
layout: post
title: Linux 性能分析初步——PART1
tags: [性能分析]
excerpt_separator: <!--more-->
---

简要结论：uptime 命令反映出的平均负载高可能是CPU繁忙、CPU竞争（线程超过逻辑核心数造成竞争/阻塞...）、I/O繁忙等多方面原因造成

<!--more-->

### 准备工作

① 安装sysstat

- 第一步：卸载原先老版本sysstat

  ```
  yum remove sysstat
  ```

- 第二步：[下载最新 rpm 包](http://sebastien.godard.pagesperso-orange.fr/download.html)，注意结尾是 rpm 不是 src.rpm

- 第三步：cd 到下载目录，执行以下命令（可通过 ls 命令查看 rpm 安装包名称）

  ```
  rpm -ivh your_package_name.rpm
  ```

② 安装 stress-ng

```
yum install -y stress-ng
```

③ 设置 MobaXterm 剪切板功能：[MobaXterm 如何更好的复制粘贴](https://blog.csdn.net/pyufftj/article/details/80467223)

④ 可选安装：htop glances dstat

### 平均负载

使用 uptime 命令（或者 watch -d uptime：2s刷新并高亮显示变化的区域）

```mysql
[root@localhost ~]# uptime
当前时间       系统以运行时间   正在登录用户数  1分钟、5分钟、15分钟平均负载
13:15:29 up   1:39,         3 users,      load average: 0.00, 0.01, 0.05
```

>  使用 man uptime 可以查看命令的帮助手册

##### 概念解释

1. 平均负载：单位时间内系统处于**可运行状态**和**不可中断状态**的平均**进程数**，即平均活跃进程数。

   - 可运行状态进程：正在使用 CPU 或者正在等待 CPU 的进程，即 ps 命令下的 R 状态进程（Running or Runnable）

   - 不可中断状态进程：处于内核态进程（例如等待I/O响应、读写磁盘数据），即 ps 命令下的 D 状态进程（Uninterruptible Sleep or Disk Sleep）

   - 内核态：在 linux 系统中，每个用户进程都有 2 个堆栈，一个用户堆栈和一个专用的内核堆栈。用户堆栈驻留在用户地址空间（32 位 x86 架构中为前 3GB）中，内核堆栈驻留在进程的内核地址空间（32 位 x86 架构中为 3GB-4GB）中。当用户进程需要执行某些特权指令（系统调用）时，它会陷入内核模式，内核会代表用户进程执行它。此执行发生在进程的内核堆栈上。

     > [作者：withshunli](https://www.zhihu.com/question/40147261/answer/163185583)

   - 平均负载 = 正在使用CPU进程数 + 等待CPU进程数 + 等待I/O进程数

     > **因此平均负载高可能是CPU繁忙、线程超过逻辑核心数、I/O繁忙等多方面原因造成**

##### 平均负载分析

uptime 给出 1分钟、5分钟、15分钟平均负载，通过三个数值可以大致分析出系统负载情况（实际上如果有实时监控曲线图更好）

- 如果1分钟、5分钟、15分钟平均负载大致相同或者相差不大，系统在15分钟内负载平稳
- 如果1分钟值远小于15分钟值，说明过去14分钟有较大负载
- 如果1分钟值远大于15分钟值，说明最近一分钟出现峰值

一般而言，如果负载超过CPU逻辑数量70%需要注意系统负载问题，当然这也只是经验值。

##### 平均负载、CPU、I/O的关系

- CPU密集型进程：会导致平均负载增高（使用 `stress-ng --cpu 2 --timeout 60` 模拟）

  > ① 使用 `mpstat -P ALL 5` 命令查看，发现部分CPU  `%idle` 很低（趋近0）但 `%iowait` 很低（5→5s频率）
  >
  > ② 使用 `pidstat -u 5 1` 可得到 %CPU 高的进程（5→采集5s、1→输出一组数据）

- I/O密集型进程：会导致平均负载增高，CPU利用率不一定高（使用 `stress-ng -i 1 --timeout 60` 模拟）

  > ① watch -d uptime 升高
  >
  > ② mpstat -P ALL 5 ：部分CPU %idle 降低（40左右），%sys 升高（50左右）
  >
  > ③  pidstat -u 5 1：stress-ng-io 进程 %CPU 和 %system 很高（78左右）

  ```javascript
  [root@localhost ~]# mpstat -P ALL 5
  Linux 3.10.0-693.el7.x86_64 (localhost.localdomain)   _x86_64_ (8 CPU)
  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
  all    0.03    0.00   10.23    0.00    0.00    0.00    0.00    0.00    0.00   89.74
    0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
    1    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
    2    0.00    0.00   19.72    0.00    0.00    0.00    0.00    0.00    0.00   80.28
    3    0.21    0.00   57.92    0.00    0.00    0.00    0.00    0.00    0.00   41.88
    4    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
    5    0.00    0.00    0.20    0.00    0.00    0.00    0.00    0.00    0.00   99.80
    6    0.00    0.00    0.20    0.00    0.00    0.00    0.00    0.00    0.00   99.80
    7    0.00    0.00    5.41    0.00    0.00    0.00    0.00    0.00    0.00   94.59
  
  ```

  ```java
  [root@localhost ~]# pidstat -u 5 1
  Linux 3.10.0-693.el7.x86_64 (localhost.localdomain)  _x86_64_ (8 CPU)
  UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
    0       651    0.20    0.40    0.00    0.00    0.60     2  vmtoolsd
    0       657    0.00    0.20    0.00    0.00    0.20     5  irqbalance
    0      1969    0.20    0.00    0.00    0.00    0.20     0  vmtoolsd
    0      4992    0.00    5.95    0.00    0.20    5.95     7  kworker/u16:2
    0      5673    0.00    4.37    0.00    0.20    4.37     6  kworker/u16:0
    0      5702    0.00   77.98    0.00    0.00   77.98     1  stress-ng-io
    0      5703    0.20    0.60    0.00    0.00    0.79     6  pidstat
  ```

  > 这里的结果和文章表述不同，文章指明 %iowait 较高，这里为 %sys，其原因是缓冲区较小频繁调用系统调用（进入内核）造成 %sys 指数偏高

- 等待CPU调度进程（大量进程or存在阻塞）：会导致平均负载+CPU使用率增高

  - 使用 stress-ng -c 14 --timeout 70 命令模拟14进程

  ```
  [root@localhost ~]# pidstat -u 5 1
  Linux 3.10.0-693.el7.x86_64 (localhost.localdomain)  _x86_64_(8 CPU)
  UID     PID    %usr %system  %guest   %wait    %CPU   CPU  Command
  0         7    0.00    3.79    0.00    0.00    3.79     -  migration/0
  0        49    0.00    0.14    0.00    0.14    0.14     -  khungtaskd
  0       651    0.14    0.00    0.00    1.22    0.14     -  vmtoolsd
  0      1321    0.00    0.14    0.00    0.14    0.14     -  master
  0      1969    0.27    0.14    0.00    1.49    0.41     -  vmtoolsd
  0      5412    0.00    0.14    0.00    0.68    0.14     -  kworker/5:2
  0      6608    0.00    0.14    0.00    0.14    0.14     -  kworker/2:1
  0      6721   47.70    0.27    0.00   36.59   47.97     -  stress-ng-cpu
  0      6722   49.05    0.41    0.00   35.50   49.46     -  stress-ng-cpu
  0      6723   47.43    0.27    0.00   37.26   47.70     -  stress-ng-cpu
  0      6724   48.10    0.14    0.00   36.59   48.24     -  stress-ng-cpu
  0      6725   46.21    0.14    0.00   38.48   46.34     -  stress-ng-cpu
  0      6726   46.07    0.27    0.00   38.35   46.34     -  stress-ng-cpu
  0      6727   49.05    0.27    0.00   35.50   49.32     -  stress-ng-cpu
  0      6728   47.56    0.27    0.00   37.13   47.83     -  stress-ng-cpu
  0      6729   45.93    0.27    0.00   38.62   46.21     -  stress-ng-cpu
  0      6730   47.56    0.27    0.00   36.99   47.83     -  stress-ng-cpu
  0      6731   46.21    0.14    0.00   38.35   46.34     -  stress-ng-cpu
  0      6732   46.48    0.27    0.00   38.21   46.75     -  stress-ng-cpu
  0      6733   46.75    0.14    0.00   38.08   46.88     -  stress-ng-cpu
  0      6734   50.95    0.27    0.00   33.74   51.22     -  stress-ng-cpu
  0      6743    0.14    9.21    0.00    9.21    9.35     -  pidstat
  ```

  > stress-ng-cpu 进程等待CPU时间 % wait 列偏高










