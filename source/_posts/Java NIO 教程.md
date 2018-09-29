---
title: Java NIO 教程
date: 2018-09-19 21:34:39
link: 180919
tags: [Java NIO]
---

本篇是Java NIO教程的第一篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)

## 什么是Java NIO

Java NIO (New IO) 是一种与Java IO和Java Networking完全不同的API。 Java NIO提供了与标准IO API不同的使用IO的方式。

## Java NIO: Channels and Buffers

在标准的IO API中，你使用字节流和字符流。在NIO中你使用通道(channel)和缓冲区(buffer)。数据总是从通道读入到缓冲区或者从缓冲区写到通道。

## Java NIO: Non-blocking IO

Java NIO使你可以执行非阻塞(non-blockiing)IO。例如，一个线程可以要求通道读取数据到缓冲区。当通道读取数据到缓冲区时，这个线程还可以做其他事情。一旦数据被读到了缓冲区，这个线程就可以继续处理它。写数据也是一样的。

### Java NIO: Selectors

Java NIO 包含选择器(selector)的概念。选择器是一个可以监视多个通道发生事件（连接打开、数据到达等)的对象。因此单个线程可以监视多个通道的的数据。

所有这些工作原理将在本系列的下一篇文章 - Java NIO概述中详细解释。