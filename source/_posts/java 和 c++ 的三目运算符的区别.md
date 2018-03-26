---
title: java 和 c++ 的三目运算符的区别
date: 2017-01-24 23:00:51
link: 17012423
tags: ['java', 'cpp']
---
# java 和 c++ 的三目运算符的区别

(之前写的文章)以前很少用java，就知道java和c++差不多。

今天就踩了一个坑。

不吐糟，直接进正文。

## 看这种写法，把较小的数加1。

```c++
int a = 5, b = 6;
b > a ? (a = 1) : b++;
```

众所周知，c++这样写是没问题的。

但是java就不行！

```shell
$ javac Solution.java
Solution.java:14: error: not a statement
        b > a ? a++ : b++;
              ^
1 error
```

## 原因

java的表达式规定只有以下四种
|type|
|:-:|
|赋值表达式|
|自增|
|方法调用|
|对象创建表达式|

然后三目运算符 不对返回值进行以上处理的话，并不能构成表达式(not a statement)

就像这样 java也报错了。

```java
int a = 5, b = 6;
        a;
```

```shell
$ javac Solution.java
Solution.java:14: error: not a statement
        a;
        ^
1 error
```
