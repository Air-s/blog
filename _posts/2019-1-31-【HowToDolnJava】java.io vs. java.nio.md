---
layout: post
title:【HowToDolnJava】java.io vs java.nio
tags: [教程翻译, JavaIO]
excerpt_separator: <!--more-->
---
[HowToDolnJava](https://howtodoinjava.com/java-io-tutorial/) 站点的 I/O 教程，Java I/O 基础知识[第二篇（原文链接）](https://howtodoinjava.com/java/io/difference-between-standard-io-and-nio/)

> (*^▽^*)

<!--more-->

## Java.IO vs. Java.NIO

JDK 1.4出现引入了新的 I/O 库——NIO。在基础 I/O 库（java.io）基础上，NIO提供高速，面向块的 I/O 操作。NIO 通过定义数据缓冲类（ByteBuffer 等等），以块为单位的形式处理数据，NIO在某种程度上得益于这种优化，而这种优化没有涉及到原生代码（native code）。

在本教程中，我将对比基础 I/O 和 NIO 关注二者最明显的差异，这样你会知道哪种 I/O 更适合你的新工程。

> 基础 I/O 位于 java.io 包中，NIO 位于 java.nio 包中，因此原文题目《Java Standard IO vs. Java NIO》译为 java.io vs java.nio 感觉更加合适【大概。

## 面向流的I/O

I/O意指信息处理系统（例如[计算机](https://zh.wikipedia.org/wiki/%E8%A8%88%E7%AE%97%E6%A9%9F)）与外部世界（可能是人类或另一信息处理系统）之间的通信接口，此接口同样可以满足程序和计算机其余组件之间的通信需求。基础 I/O 设计者使用“流”进行比喻，所有 I/O 都被视为单个字节的移动（一个输入流每次会读入一个字节的数据，一个输出流同样每次消费一个字节的数据）。面向流 I/O 不仅可用于外部通信，它同样在程序内部中使用，其用途在于对象与字节之间的转换（将对象转换为字节，将字节转换为对象）。

> 但是每次读取一个字节是非常低效的操作（陷入内核态调用方法会消耗大量时间），因此实际上会使用 Bufferedxxx 类进行装饰。

## NIO

创建 NIO 是为了让 Java 程序员无需接触原生代码（native code）即可实现高速 I/O。NIO 将最耗时的 I/O 操作（填充和释放缓冲区）移回操作系统，从而大大提高了速度。

> 且不说 NIO 使用上是否有难度，NIO 的 API 设计很值得借鉴，值得学习。


## 传统IO和NIO之间的差异

#### 1）IO流(stream)与NIO数据块(blocks)

基础 I/O 与 NIO 之间最重要的区别与数据的打包和传输方式。基础 I/O 面向流（字节）处理数据（以字节为单位），而 NIO 以数据块为单位处理数据。

基础 I/O 一次处理一个或多个字节的数据。例如输入流产生一个字节的数据，输出流则可以消耗一个字节的数据。在这种机制下为流数据创建过滤器则非常容易。将几个过滤器链接在一起也相对简单，这样每个过滤器可以完成复杂处理的一部分，组合在一起可以完成较为复杂的处理工作。

但需要注意的使用基础I/O字节不会缓存在任何地方。此外，您无法在流中的数据中前后移动（使用游标譬如在想要的位置读取等等）。如果需要在从流中读取的数据中前后移动，则必须先将其缓存在缓冲区中。

> 这里表述的比较含糊，大意应该是在用户调用 API 读取字节前系统并没有做预读，因此当用户需要读取字节流时需要“当场”去读，读取时是阻塞的，当读取完毕时立即返回读取的值。这样的机制必然无法在数据中前后移动，因为数据被“切割”成字节形式，而且每次读取都是立即返回（可以想象刀削面【饿了

NIO 每个操作每一步生成或消耗一个数据块。以块为单位处理数据比面向字节处理的方式要快得多。用户可以根据需要在缓冲区中前后移动（使用游标），这使用户在处理数据过程中具有更大的灵活性。

但是，您还需要检查缓冲区是否包含完整处理所需的所有数据。并且，您需要确保在将更多数据读入缓冲区时，不要覆盖尚未处理的缓冲区中的数据。但是 NIO  缺乏基础 I/O 的优雅和简单性。

> 更进一步：[3 Ways to Read Files – Java NIO](https://howtodoinjava.com/java7/nio/3-ways-to-read-files-using-java-nio/)

#### 2）同步与异步IO

基础 I/O 的方法是阻塞或同步的。这意味着，当线程调用 read() 或 write() 时，该线程将被阻塞，直到方法返回数据，或者数据被完全写入，在此期间，该线程将处于阻塞状态。为防止阻塞（不只为了I/O）在现代语言中会引入多线程缓解这种情况。

在异步IO中，线程可以请求将某些数据写入通道，之后线程可以继续做其他事情无需等待方法完成。通常一些线程进行IO调用，但在其他通道上执行实际的IO操作。也就是说，单个线程现在可以管理多个输入和输出通道。

异步I/O中的核心对象称为 Selector。Selector 是用户注册各类 I/O 事件的地方，通过它可以告知用户这些事件何时发生。所以，我们需要做的第一件事是创建一个 Selector：

```java
Selector selector = Selector.open();
```

稍后我们将在各种通道（FileChannel）上调用 register() 方法注册 I/O 事件，register() 的第一个参数始终是 Selector。

> 除去此网站的 NIO 教程，[Java NIO 详解](https://blog.csdn.net/suifeng3051/article/details/48160753)、[Java 进阶（五）Java I/O 模型从 BIO 到 NIO 和 Reactor 模式](https://zhuanlan.zhihu.com/p/31321140) 这两篇文章同样值得一读。

#### 3）基础IO API vs NIO API

使用基础IO：这里使用 BufferedReader 装饰 FileReader 类，并没有直接使用  FileReader 类。

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
  
public class WithoutNIOExample
{
    public static void main(String[] args)
    {
        BufferedReader br = null;
        String sCurrentLine = null;
        try
        {
            br = new BufferedReader(new FileReader("test.txt"));
            while ((sCurrentLine = br.readLine()) != null)
            {
                System.out.println(sCurrentLine);
            }
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        finally
        {
            try
            {
                if (br != null)
                	br.close();
            } catch (IOException ex)
            {
                ex.printStackTrace();
            }
        }
    }
}
```

使用NIO

```java
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
  
public class ReadFileWithFixedSizeBuffer
{
    public static void main(String[] args) throws IOException
    {
        RandomAccessFile aFile = new RandomAccessFile("test.txt", "r");
        FileChannel inChannel = aFile.getChannel();
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        while(inChannel.read(buffer) > 0)
        {
            buffer.flip();
            for (int i = 0; i < buffer.limit(); i++)
            {
                System.out.print((char) buffer.get());
            }
            buffer.clear(); // do something with the data and clear/compact it.
        }
        inChannel.close();
        aFile.close();
    }
}
```



## 总结

NIO允许您仅使用一个（或更少）线程来管理多个通道，但解析数据可能比基础IO更复杂一些。

如果需要同时管理数千个打开的连接，每个只发送一些数据，例如聊天服务器，在NIO中实现服务器可能是一个优势。同样，如果需要与其他计算机保持大量打开的连接，例如在P2P网络中，使用单个线程来管理所有出站连接可能是一个优势。（也就是存在大量连接用 NIO 准没错）

如果仅有少量连接在网络环境良好的情况下需要一次发送大量数据，则应该选择传统 IO 实现。