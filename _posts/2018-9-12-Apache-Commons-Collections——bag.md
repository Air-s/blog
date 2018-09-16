---
layout: post
title: Apache Commons Collections——Bag
tags: [Apache Commons, Collections, Utils, Bag]
excerpt_separator: <!--more-->
---
Apache Commons Collections——Bag
<!--more-->
## 概览
![Collection Bag API](https://i.imgur.com/nLMJGVZ.jpg)
> JavaDoc：[apache.commons.collections4.bag](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/index.html)

CLass Summary 中列举的类很多，当然是不能一个个去看的。注意到上方的提示：This package contains implementations of the Bag and SortedBag interfaces. See: Description。大意是此包是 Bag 和 SortedBag 接口的实现。然后查看 Description 内容。

![Collection Bag API Description](https://i.imgur.com/F1U005B.jpg)
上述Description大意是：
- Bag 定义
- 两种实现类：hashBag、Treebag
- 五种装饰器：Synchronized、Unmodifiable、Predicated、Transformed、Collection

> 了解这些信息后，结合 Class Summary 中的 Description 大体就可以明白这些类的作用。除去基本的 HashBag 和 TreeBag，大部分类都是装饰器类。例如 PredicateBag 可以对 Bag 进行功能装饰，SynchronizedSortedBag 针对 SortedBag 进行功能装饰。这些装饰器的方法都是静态的。 

## HashBag、TreeBag
首先从 Google 中获取一波资料，之后需要继续深入阅读文档

### 基本概念
- Bag：继承自 Collection 接口，定义了一个集合，该集合会记录对象在集合中出现的次数。假设你有一个包，包含 {a, a, b, c}。调用getCount(a) 方法将返回2，调用 uniqueset() 方法将返回 {a, b, c}。
> [commons-collections使用介绍之Bag](https://blog.csdn.net/jianggujin/article/details/51069087)
- TreeBag：它会跟踪对象添加到对象的副本数量，并按计数的升序对其进行排序。要获得最高计数项，只需调用last()方法即可。
> [像HashMap这样的集合，但排序如何处理？](https://oomake.com/question/365265)
- Bag 接口中两个最重要的方法是：getCount(Object o)，用来返回Bag里面特定对象的出现次数；uniqueSet()，返回所有唯一元素。因此常见的一种用法是统计单词频率

Word          | Frequency
------------- | -------------
a  			  |   1
and  		  |   5
appearances   |   1
as            |   1

> [【CODE Q&A Sovled】您将使用哪种数据结构：TreeMap或HashMap？（JAVA）](https://code.i-harness.com/zh-CN/q/49d23)

### 关于文档
从文档中能得到很多信息，从头开始看
- 继承关系
	- Type Parameters：泛型代表意义
	- All Implemented Interfaces：继承接口
- 文字说明
- Nested Class Summary：内部类
	- AbstractMapBag.MutableInteger 静态类，查看源码解释作用为存储数据
- Constructor Summary：构造方法一览
	- 对于 TreeBag 可以传入一个 Comparator 实现自定义比较
- Method Summary
	- 这个就比较重要
	- 首先是这个类自己的方法
	- 其次是继承下来的方法（Methods inherited from class ...）
- Constructor Detail/Method Detail：没啥特别重要的说明

### 初步使用
- Bag 接口 API 方法摘要、HashBag/TreeBag 简单 Demo
> [commons-collections 使用介绍之 Bag](https://blog.csdn.net/jianggujin/article/details/51069087)
- Github 上的一个 Demo 项目，涉及到HashBag、TreeBag、SynchronizedBag、SynchronizedSortedBag 使用示例
> [【Github】Vedenin：java_in_examples](https://github.com/Vedenin/java_in_examples/tree/master/collections/src/com/github/vedenin/eng/collections/multiset)

### 装饰器 PredicatedBag/TransformedSortedBag

![](https://i.imgur.com/bQzYV9S.jpg)
![](https://i.imgur.com/D2vyYjT.jpg)
> JavaDoc：[Class PredicatedBag](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/org/apache/commons/collections4/bag/PredicatedBag.html)

以 PredicatedBag 为例，从文字简述中可以看到静态方法需要两个参数，其中第二个参数为 Predicate 类，查看其定义

![](https://i.imgur.com/zjziKKH.jpg)
> JavaDoc：[Interface Predicate](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/org/apache/commons/collections4/Predicate.html)

重要的一句话：Standard implementations of common predicates are provided by PredicateUtils.

![](https://i.imgur.com/zt2VWtX.jpg)
> JavaDoc：[Class PredicateUtils](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/org/apache/commons/collections4/PredicateUtils.html)

文字表述的很清楚，PredicateUtils 类具体方法见 JavaDoc 文档

![](https://i.imgur.com/G8Lnckm.jpg)
> JavaDoc：[Class TransformerUtils](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/org/apache/commons/collections4/TransformerUtils.html)

TransformerUtils 类具体方法见 JavaDoc 文档