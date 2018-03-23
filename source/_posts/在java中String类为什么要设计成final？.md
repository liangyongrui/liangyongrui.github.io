---
title: 在java中String类为什么要设计成final？
date: 2017-06-21 10:40:51
tags: java
---
# 在java中String类为什么要设计成final？

## String本质上是一个final类

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
```

内部是一个 private final char数组，内部的所有方法都没有对这个char[]中的值进行修改。

所以外部看起来他是不变的。

## 不变的好处

例如在想得到一个字符串的追加串。

```java
 //不可变的String
public static String appendStr(String s) {
    s += "bbb";
    return s;
}

//可变的StringBuilder
public static StringBuilder appendSb(StringBuilder sb) {
    return sb.append("bbb");
}
```

用String就可以避免原串的改变，因为后面很可能会用到。

还有就是在HashMap中会经常用到，这种对key有唯一性约束的数据结构，key一定要是immutable。如果key是mutable那个很可能在你不小心的操作中破坏这种唯一性。

最后就是，字符串有一个字符串常量池的东西，这可以大大节省内存。然而要实现字符串常量池就基本的操作就是把String设置成immutable。

参考资料:[在java中String类为什么要设计成final？](https://www.zhihu.com/question/31345592)
