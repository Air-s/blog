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
CLass Summary 中列举的类很多，当然是不能一个个看的。注意到上方的提示：This package contains implementations of the Bag and SortedBag interfaces. See: Description。大意是此包是 Bag 和 SortedBag 接口的实现。然后查看 Description 内容。
> JavaDoc：[apache.commons.collections4.bag](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/index.html)

![Collection Bag API Description](https://i.imgur.com/F1U005B.jpg)
上述Description大意是：
- Bag 定义
- 两种实现类：hashBag、Treebag
- 五种装饰器：Synchronized、Unmodifiable、Predicated、Transformed、Collection

了解这些信息后，结合 Class Summary 中的 Description 大体就可以明白这些类的作用。除去基本的 HashBag 和 TreeBag，大部分类都是装饰器类。例如 PredicateBag 可以对 Bag 进行功能装饰，SynchronizedSortedBag 针对 SortedBag 进行功能装饰。这些装饰器的方法都是静态的。 