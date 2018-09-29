---
title: Java NIO Channel
date: 2018-09-29 15:10:14
link: 180929
tags: [Java NIO]
---

本篇是Java NIO教程的第三篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)
1. [java NIO Channel](http://www.liangyongrui.com/posts/180929)
1. [java NIO Buffer](http://www.liangyongrui.com/posts/180929-1)

## 概述

Java NIO Channels 很像流，他们有一些不同：

* 你可以对Channels边读边写。而流通常是单向的。
* Channels可以进行异步的读写。
* Channels总是从一个Buffer中读取或写入。

如图所示，你可以把Channel中的数据读到Buffer中，也可以把Buffer中的数据写入到Channel：
{% asset_img overview-channels-buffers.png Java NIO: Channels read data into Buffers, and Buffers write data into Channels %}

## Channel的实现类

下面四个是在Java NIO中最重要的Channel实现类:

* FileChannel
* DatagramChannel
* SocketChannel
* ServerSocketChannel

*FileChannel*用于对文件进行数据读写。

*DatagramChannel*可以通过UDP来读写数据。

*SocketChannel*可以通过TCP来读写数据。

*ServerSocketChannel*允许你监听正在过来的tcp连接，类似于web服务器做的事情。对于每个正在过来的连接，会有一个*SocketChannel*被创建。

## 基础的Channel例子

下面是一个用*FileChannel*来读取一些数据到Buffer中的例子：

```java
// java 11
public static void main(String[] args) {
    try (var file = new RandomAccessFile("data/test.txt", "rw")) {
        FileChannel inChannel = file.getChannel();
        ByteBuffer buf = ByteBuffer.allocate(48);
        while (true) {
            int bytesRead = inChannel.read(buf);
            System.out.println("Read " + bytesRead);
            if (bytesRead == -1) {
                break;
            }
            buf.flip();
            while (buf.hasRemaining()) {
                System.out.print((char) buf.get());
            }
            buf.clear();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

注意*buf.flip()*的调用。首先你把数据读取到Buffer，然后你flip它，然后你又把数据从buffer中读出来。下一篇文章中你将会看到更多关于*Buffer*的使用细节。