---
layout: post
title: CODE Q&A Solved 一些有趣的问答（IO 与 集合）
tags: [基础]
excerpt_separator: <!--more-->
---

不想干正事时的好去处

<!--more-->

### I/O

1. [java java按行写入文件 java读取文件第一行 - 如何使用Java逐行读取大型文本文件？](https://code.i-harness.com/zh-CN/q/598b51)

   > 其中一个回答记录并测试了[10种不同的方式来读取Java文件](https://funnelgarden.com/java_read_file/) ，可参考

2. [【官方示例】File IO（NIO.2）：（不同场景下）读、写并创建文件](https://blog.csdn.net/hhx0626/article/details/78183928)

   > [官方文档](https://docs.oracle.com/javase/tutorial/essential/io/file.html) 出品，必属精品。介绍了不同场景下应该使用何种 API

3. [java 判断文件夹是否存在 java删除文件 - 如何检查Java中是否存在文件？](https://code.i-harness.com/zh-CN/q/1bb861)

4. [java inputstream读取字符串 inputstream转为string - 如何在Java中将字符串转换为InputStream？](https://code.i-harness.com/zh-CN/q/bef62)

5. [如何将 java.io.File 转换为 byte[] ？](https://code.i-harness.com/zh-CN/q/d1b64)

6. [java遍历目录下的所有文件](https://code.i-harness.com/zh-CN/q/1c25d0)

7. [将输出从java.io.OutputStream传递到Java中的String的最佳方式是什么？](https://code.i-harness.com/zh-CN/q/34f3e)

8. [使用scanner java的scanner类与换行符问题](https://code.i-harness.com/zh-CN/q/c7ebdd)

9. [如何在Java中获得依赖于平台的换行符？](https://code.i-harness.com/zh-CN/q/32c4b)

10. [在Java中安全地将long转换为int](https://code.i-harness.com/zh-CN/q/18462f)

11. 

### 集合

1. [java list教程 list用法 - 如何在Java中创建一个新的List](https://code.i-harness.com/zh-CN/q/d19cc)

2. [list合并 - 我如何在Java中加入两个列表？](https://code.i-harness.com/zh-CN/q/2e477)

3. [【相互转换】java数组转set set转string - 如何将数组转换为Java中的Set](https://code.i-harness.com/zh-CN/q/2ec267)

4. [在Java中将 List 转换为 Set 的最简单方法是什么？](https://code.i-harness.com/zh-CN/q/15d164)

5. [如何在迭代集合时安全地从集合中移除元素](https://code.i-harness.com/zh-CN/q/12422a)

6. [所有迭代列表的方式以及每种方式的优缺点](https://code.i-harness.com/zh-CN/q/118ea33)

7. [**int[]**如何轻松转换为**Integer[]**](https://code.i-harness.com/zh-CN/q/d6fc5)

8. [在Java中如何将**List <Integer>**转换为**int[]**？](https://code.i-harness.com/zh-CN/q/ea7af)

9. [是否有一个好的可用（标准Java）数据结构来表示Java中的树？](https://code.i-harness.com/zh-CN/q/35bf96)

10. [ 如何从ArrayList中删除重复的元素？](https://code.i-harness.com/zh-CN/q/31cd0)

11. [java逗号分隔字符串转为集合](https://code.i-harness.com/zh-CN/q/724483)

12. [如何检测链表中的循环？](https://code.i-harness.com/zh-CN/q/28a2cb)

13. [检测重复String的简单方法](https://code.i-harness.com/zh-CN/q/12d8eb)

14. [Java 8中的map和flatMap方法有什么区别？](https://code.i-harness.com/zh-CN/q/1972c92)

    > [flatmap将多个Stream连接成一个Stream](https://blog.csdn.net/andyzhaojianhui/article/details/79047825)，换言之它可以接收类似于list<list<E>>之类的集合，每个元素都可以迭代。之后对每一个元素进行迭代，每一次迭代进行一次方法

15. [为什么在尝试从列表中删除元素时会遇到UnsupportedOperationException？](https://code.i-harness.com/zh-CN/q/2d40f3)

    > ```java
    > Arrays.asList()返回的列表是不可变的。
    > 使用 List<String> list = new ArrayList(Arrays.asList(split))
    > ```


16. [如何在Java中填充字符串](https://code.i-harness.com/zh-CN/q/5ed6d)

    > 使用 String.format 方法

17. [如何在Java中反转字符串](https://code.i-harness.com/zh-CN/q/737fb7)

    > ```java
    > new StringBuilder(hi).reverse().toString()
    > ```

18. [java如何初始化二维数组](https://code.i-harness.com/zh-CN/q/baa31d)

19. 