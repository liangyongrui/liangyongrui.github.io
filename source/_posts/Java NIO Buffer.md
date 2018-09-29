---
title: Java NIO Buffer
date: 2018-09-29 15:10:14
link: 180929
tags: [Java NIO]
---

本篇是Java NIO教程的第四篇。

## 目录

1. [java NIO 教程](http://www.liangyongrui.com/posts/180919)
1. [java NIO 概述](http://www.liangyongrui.com/posts/180919-1)
1. [java NIO Channel](http://www.liangyongrui.com/posts/180929)
1. [java NIO Buffer](http://www.liangyongrui.com/posts/180929-1)

## 概述

Java NIO Buffers 被用来配合NIO的Channels来使用。正如你所知道的，数据可以从channels读到buffers，还可以从buffers写到channels。

一个buffer本质上就是一块用于保存数据的内存。这个内存被包裹成了NIO的Buffer对象，然后这个对象提供了一些列方便我们操作这块内存的方法。

## 基础的Buffer用法

使用*Buffer*读或写数据主要有四步：

* 写数据到*Buffer*中
* 调用*buffer.flip()*
* 从*Buffer*中读出数据
* 调用*buffer.clear()*或*buffer.compact()*

当你写数据到buffer的时候，buffer会保持记录你写了多少数据。一旦你读取数据，你需要使用*flip()*方法把buffer从*写模式*切换到*读模式*。在*读模式*下，buffer允许您读取写入buffer的所有数据。

一旦你读完了有所数据，你需要清空buffer，使它准备再次被写入数据。你有两种办法做这件事：调用*clear()*或调用*compact()*。*clear()*将会清空整个buffer。*compact()*方法只会清空你已经读的。所有没读的数据将会被移动到buffer的开头，然后数据将会从没读的数据之后写入。

和上一节看到的代码一样，下面是一个简单的*Buffer*用法：

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

## Buffer的容量、位置和限制

为了理解*Buffer*是如何工作的，*Buffer*有三个你需要熟悉的属性，后面我直接用英文描述：

* 容量(capacity)
* 位置(position)
* 限制(limit)

*position*和*limit*的含义取决于*Buffer*是*读模式*还是*写模式*。*Capacity*的含义一直都一样，和buffer的模式无关。

下面有一张图，解释了capacity, position和limit在不同模式下的意义

{% asset_img buffers-modes.png Buffer capacity, position and limit in write and read mode. %}

### Capacity

作为存储器块，buffer具有一定的固定大小。 您只能将一定容量字节，长整数，字符等写入buffer。 buffer已满后，您需要清空它（读取数据或清除它），然后才能将更多数据写入其中。

### Position

将数据写入buffer时，可以在某个position执行此操作。 最初position 为 0。当一个字节，长整数等已写入buffer时，position被提前指向buffer中的下一个单元以便后面再次插入数据。 position最大可以变为容量 - 1。

从buffer读取数据时，也可以从给定position读取数据。 当你将buffer从*写模式*翻转到*读模式*时，position将重置为0.当您从buffer读取数据时，您将从position读取数据，并将position提前到下一个要读取的位置。

### Limit

在*写模式*下，Buffer的limit是您可以写入缓冲区的数据量的限制。 在*写模式*下，*limit*等于buffer的*capacity*。

将buffer翻转为*读模式*时，*limit*意味着你可以从数据中读取的数据量的限制。 因此，当将Buffer翻转到*读模式*时，limit被设置为*写模式*的position。 换句话说，你可以读取和写入的字节数一样多的字节。

## Buffer的类型

Java NIO 附带了这些Buffer类型：

* ByteBuffer
* MappedByteBuffer
* CharBuffer
* DoubleBuffer
* FloatBuffer
* IntBuffer
* LongBuffer
* ShortBuffer

正如你看到的，这些Buffer类型代表了不同的数据类型。换句话说，他们可以让你在buffer中使用char, short, int, long, float 或 double。

## 分配一个Buffer

想要得到一个buffer你你一步必须是分配它。每一个Buffer类都有一个*allocate()*方法来做这件事。下面有个例子在展示如何得到一个容量为48bytes的*ByteBuffer*

```java
ByteBuffer buf = ByteBuffer.allocate(48);
```

下面有个例子在展示如何得到一个空间可以存放1024个字符的*CharBuffer*

```java
CharBuffer buf = CharBuffer.allocate(1024);
```

## 往Buffer中写入数据

你有两种办法往Buffer中写入数据：

把数据从*Channel*写到*Buffer*

```java
int bytesRead = inChannel.read(buf); //read into buffer.
```

用buffer的*put()*方法，把数据写到Buffer中

```java
buf.put(127);
```

*put()*方法有许多不同的版本，它可以让你把数据写到*buffer*中用不同的方式。例如：写到一个特定的位置，或者写一个byte数组到buffer中。具体看JavaDoc吧。

## flip()

*flip()*方法用于交换*buffer*的读写模式。position记录从哪里开始读，limit记录可以读到哪。可以参考下面的源码

```java
//java 11
public Buffer flip() {
    limit = position;
    position = 0;
    mark = -1;
    return this;
}
```

## 从Buffer中读取数据

有两种办法从Buffer中读取数据：

把数据从Buffer读到Channel

```java
int bytesWritten = inChannel.write(buf);
```

用buffer自带的*get()*方法

```java
byte aByte = buf.get();
```

和*put()*一样，*get()*也有很多的版本。例如：从特定的position读取，或读取一个byte数组。

## rewind()

*Buffer.rewind()*让position回到0，所以你可以在buffer中重读一些数据。从而实现反复读取，可以参考源码：

```java
public final Buffer rewind() {
    position = 0;
    mark = -1;
    return this;
}
```

## clear() 和 compact()

一旦你读完了，你需要把buffer的*读模式*换成*写模式*。所以你得用*clear()*或*compact()*方法。

如果你调用了*clear()*，*position*将回到0，*limit*将等于*capacity*。换句话说，buffer被清空了，事实上buffer中的数据还在，只是指针变了。

如果你的buffer中还有没读的数据，然后你调用了*clear()*，这些数据将会被*遗忘*。如果你只是暂时不想读，你需要用*compact()*方法来切换成*写模式*。*compact()*方法只会清空你已经读的。所有没读的数据将会被移动到buffer的开头，然后数据将会从没读的数据之后写入。

## mark() 和 reset()

你可用用*mark()*方法来标记一个位置，然后用*reset()*方法回到这个位置。从而实现局部反复读取：

```java
buffer.mark();
//call buffer.get() a couple of times, e.g. during parsing.
buffer.reset();  //set position back to mark.
```

可以参考源码：

```java
public final Buffer mark() {
    mark = position;
    return this;
}

public final Buffer reset() {
    int m = mark;
    if (m < 0)
        throw new InvalidMarkException();
    position = m;
    return this;
}
```

## equals() 和 compareTo()

可以用 *equals()* 和 *compareTo()* 来比较两个buffer。

### equals()

如果两个buffer是相等的，必须满足：

1. 他们是相同的类型；
2. 保存剩余的内容都相同；
3. 剩余的大小也相同。

可以看出，两个buffer相同并不是所有的元素都要相同，只要两个buffer中剩余的内容都相同即可。

### compareTo()

如果一个buffer比另一个buffer小，需要满足：

1. 第一个不同的元素小于另一个buffer;
2. 所有元素都相等，但是第一个buffer的元素先耗尽。

这个可以类比两个String的compareTo。

具体细节可以看源码：

```java
public final int remaining() {
    return limit - position;
}

public boolean equals(Object ob) {
    if (this == ob)
        return true;
    if (!(ob instanceof CharBuffer))
        return false;
    CharBuffer that = (CharBuffer)ob;
    if (this.remaining() != that.remaining())
        return false;
    int p = this.position();
    for (int i = this.limit() - 1, j = that.limit() - 1; i >= p; i--, j--)
        if (!equals(this.get(i), that.get(j)))
            return false;
    return true;
}

public int compareTo(CharBuffer that) {
    int n = this.position() + Math.min(this.remaining(), that.remaining());
    for (int i = this.position(), j = that.position(); i < n; i++, j++) {
        int cmp = compare(this.get(i), that.get(j));
        if (cmp != 0)
            return cmp;
    }
    return this.remaining() - that.remaining();
}
```