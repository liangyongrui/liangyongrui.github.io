---
title: Java NIO 概述
date: 2018-09-19 22:34:39
link: 180919-1
tags: [Java NIO]
---

本篇是Java NIO教程的第二篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)

## 概述

上一篇文章我们介绍了 Java NIO 由下面三种核心组建构成

* Channels
* Buffers
* Selectors

Java NIO 有更多的类和组建，但是这三个是最核心的API。其余的组建，例如*Pipe*和*FileLock*只不过是一些被用来在连接这三个核心组件的通用的类。因此在NIO概述中，我将关注这三个组建。其他组建将在本教程的其他文章中进行解释。具体见目录（如果目录中没有又想学习的可以留言）。

## Channels and Buffers

典型的，在NIO中所有的IO都是从*Channel*开始的。*Channel*有点像流(stream)。*Channel*中的数据可以被读到*Buffer*中。数据也可以从*Buffer*写到*Channel*中。如图所示：
{% asset_img overview-channels-buffers.png Java NIO: Channels read data into Buffers, and Buffers write data into Channels %}

有几种*Channel*和*BUffer*类型。下面是主要的*Channel*实现类:

* FileChannel
* DatagramChannel
* SocketChannel
* ServerSocketChannel

正如你看到的，这些通道覆盖了UDP+TCP network IO 和 文件IO。

伴随着这些类还有一些有趣的接口，但是为了保持这篇概述的简单性，这里先不列出，在Java NIO教程的其他文章中，慢慢的解释他们。

下面是一些*Buffer*的实现类：

* ByteBuffer
* CharBuffer
* DoubleBuffer
* FloatBuffer
* IntBuffer
* LongBuffer
* ShortBuffer

这些*Buffer*覆盖了可以通过IO发送的基本数据类型：byte，short，int，long，float，double和characters。

Java NIO还有一个*MappedByteBuffer*，它与内存映射文件一起使用。 我将把这个*Buffer*从这个概述中删除。

## Selectors

一个*Selector*可以在单个线程中控制多个*Channel*。如果你的应用有很多的连接(Channels)打开，但是每个连接只有很少的流量(low traffic)，用*Selector*将会很方便。例如一个聊天服务器。

这有个插图，在一个线程中使用一个*Selector*控制三个*Channel*

{% asset_img overview-selectors.png Java NIO: A Thread uses a Selector to handle 3 Channel's %}

你需要使用一个*Selector*去注册一个*Channel*。然后你可以调用他的*select()*方法。这个方法将会阻塞，直到其中一个被注册的*channel*准备就绪。例如：传入连接、接收数据等。