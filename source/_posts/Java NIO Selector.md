---
title: Java NIO Selector
date: 2018-10-30 14:02:48
link: 181030
tags: [Java NIO]
---

本篇是Java NIO教程的第七篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)
1. [java NIO Channel](http://www.liangyongrui.com/posts/180929)
1. [java NIO Buffer](http://www.liangyongrui.com/posts/180929-1)
1. [Java NIO 分散和聚集](http://www.liangyongrui.com/posts/180930)
1. [Java NIO Channel向Channel传输数据](http://www.liangyongrui.com/posts/181002)

## 概述

*Selector* 用来检查一个或多个NIO *Channel*，然后决定用那个*channel*来进行操作（例如读写）。这样，单个线程可以管理多个*Channel*，从而管理多个网络连接。

## 为什么要使用 Selector

切换线程是一个非常昂贵的动作，你使用*Selector*可以只使用一个线程控制多个*Channel*。

但是，随着cpu和现代操作系统越来越好，这样的开销越来越少，甚至对于多核处理器，多线程可能会更好。但是*Selector*可以帮你做到一个线程控制多个*Channel*。

如图所示，在一个线程中，用Selector控制三个*Channel*

{% asset_img overview-selectors.png Java NIO: A Thread uses a Selector to handle 3 Channel's %}

## 创建一个 Selector

你可以用*Selector.open()*方法创建，像这样：

```java
Selector selector = Selector.open();
```

## 使用 Selector 注册 Channel

为了能够用*Selector* 来操作 *Channel*，你需要对Channel进行注册。可以像这样使用*SelectableChannel.register()*方法：

```java
channel.configureBlocking(false);
SelectionKey key = channel.register(selector, SelectionKey.OP_READ);
```

这里的*Channel*必须在非阻塞模式下才能和*Selector*一起使用。这意味着你不能把*FileChannel*和*Selector*一起使用，因为*FileChannel*不能切换到非阻塞模式。Socket Channel可以很好的使用。

注意*register*的第二个参数。第二个参数表示了，你对这个*Channel*哪些操作比较感兴趣。有四种不同的事件可以监听:

* Connect
* Accept
* Read
* Write

*Channel*执行某个事件的前提，是他已经*ready*了这个事件。这四个事件可以用*SelectionKey*中的常量来表示：

* SelectionKey.OP_CONNECT
* SelectionKey.OP_ACCEPT
* SelectionKey.OP_READ
* SelectionKey.OP_WRITE

如果对超过一个事件感兴趣，可以用位运算或

```java
int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;
```

## SelectionKey's

