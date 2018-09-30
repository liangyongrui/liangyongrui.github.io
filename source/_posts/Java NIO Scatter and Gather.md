---
title: Java NIO 分散和聚集
date: 2018-09-30 09:06:53
link: 180930
tags: [Java NIO]
---

本篇是Java NIO教程的第五篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)
1. [java NIO Channel](http://www.liangyongrui.com/posts/180929)
1. [java NIO Buffer](http://www.liangyongrui.com/posts/180929-1)
1. [Java NIO 分散和聚集](http://www.liangyongrui.com/posts/180930)

## 概述

Java NIO 内置了分散(Scatter)和聚集(Gather)的支持，分散和聚集是一种从channel读写数据的概念。

分散读(Scattering Reads)是一种把数据从channel读到多个buffer的操作。

聚集写(Gathering Writes)是一种把数据从多个buffer写到channel的操作。

如果你需要把多块数据分别传输，分散和聚集就特别的有用。例如：如果一条信息是由header和body组成，你需要保持他们在不同buffer中，因为这样你可以分别对他们做一些操作。

## 分散读

分散读可以把数据从channel读到多个buffer，如图：

{% asset_img scatter.png Java NIO: Scattering Read %}

下面一段代码展示了如何分散读：

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body   = ByteBuffer.allocate(1024);

ByteBuffer[] bufferArray = { header, body };

channel.read(bufferArray);
```

buffer数组作为参数，传入*channel.read()*方法，这个方法会按照顺序读满一个再读下一个。

因为分散读是把一个buffer装满才去装下一个，这就意味着不满足动态大小的消息部分。如果你的消息有header和body，那么你的header的大小应该是固定的（例如128 bytes），这样分散读才有意义。

## 聚集写

聚集写可以把数据从多个buffer写到channel，如图：

{% asset_img gather.png Java NIO: Gathering Write %}

下面一段代码展示了如何聚集写：

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body   = ByteBuffer.allocate(1024);

ByteBuffer[] bufferArray = { header, body };

channel.write(bufferArray);
```

buffer数组作为参数，传入*channel.write()*方法，这个方法会按照顺序写完一个再写下一个。

聚集写只会去把buffer中存在的内容写入，所以聚集写可以和动态大小的消息一起工作。
