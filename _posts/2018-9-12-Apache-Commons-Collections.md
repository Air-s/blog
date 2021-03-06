---
layout: post
title: Apache Commons Collections
tags: [Apache Commons, Collections, Utils]
excerpt_separator: <!--more-->
---
Apache Commons Collections 工具包总览
<!--more-->
## 概览
![Apache Commons Collections 4.2 API](https://i.imgur.com/lmUDhvb.jpg)

于是乎决定看一下 Apache Commons 工具包。原因基于一个常识：部分情况下自己编写的代码都已经有成熟的库实现。
> Apache Commons 工具包官方网站：[Apache Commons](https://commons.apache.org/)

学习顺序首先基于更新时间，其次是实用度。例如 Collections、IO、Lang 是必须要看的，其他工具包例如 Codec、FileUpload 等亦可作为知识补充。于是决定决定先学习 Collections 工具包。

从 [Collections 4.2 API Overview](https://commons.apache.org/proper/commons-collections/javadocs/api-4.2/index.html)  中看到有 N 多 Package，查看其 Description 大部分译为
- 提供对特定接口的实现(类)
> This package contains implementations of the ... interfaces
- 提供一些功能类
> This package provides classes ...
- 解释一波 split map 概念
> The "split map" concept is that of an object that implements the Put and Get interfaces, with differing generic types.