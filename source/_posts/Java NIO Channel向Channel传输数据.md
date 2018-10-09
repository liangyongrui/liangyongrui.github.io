---
title: Java NIO Channel向Channel传输数据
date: 2018-10-02 11:42:59
link: 181002
tags: [Java NIO]
---

本篇是Java NIO教程的第六篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)
1. [java NIO Channel](http://www.liangyongrui.com/posts/180929)
1. [java NIO Buffer](http://www.liangyongrui.com/posts/180929-1)
1. [Java NIO 分散和聚集](http://www.liangyongrui.com/posts/180930)
1. [Java NIO Channel向Channel传输数据](http://www.liangyongrui.com/posts/181002)

## 概述

在Java NIO中 如果你使用的是*FileChannel*，你可以把数据从一个channel传输到另一个channel。*FileChannel*类的*transferTo()*和*transferFrom*可以做到。

## transferFrom()

*FileChannel.transferFrom()*方法可以吧数据从源channel传输到*FileChannel*。下面是一个简单的例子：

```java
// java 11
var fromFile = new RandomAccessFile("fromFile.txt", "rw");
FileChannel fromChannel = fromFile.getChannel();

var toFile = new RandomAccessFile("toFile.txt", "rw");
FileChannel toChannel = toFile.getChannel();

long position = 0;
long count = fromChannel.size();

toChannel.transferFrom(fromChannel, position, count);
```

*position*代表目的文件开始的位置，*count*表示要传输的数据大小，如果*fromChannel*中的数据量比count小的话，就全部传输。

