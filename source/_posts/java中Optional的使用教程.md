---
title: java中Optional的使用教程
date: 2018-08-25 22:41:04
link: 180826
tags: [java8, java9]
---

## 1.概述

本文将展示java8中的新类Optional。并介绍在java9中增加的三个方法。

这个类的目的是提供一个类型级别的解决方案来表示可选值，而不是用null引用。

使用Optional之前需要引入：

```java
import java.util.Optional;
```

## 2.创建Optional对象

有很多种方法创建Optional对象。创建一个空的Optional对象：

```java
Optional<String> empty = Optional.empty();
```

我们可以创建一个空的Optional对象用静态的*of*API：

```java
String name = "baeldung";
Optional.of(name);
```

但是，*of*方法的参数不能为null，否则，我们将得到一个NullPointerException：

```java
String name = null;
Optional<String> opt = Optional.of(name);
```

但是，如果我们期望传入可能有空值的参数，我们可以使用*ofNullable* API：

```java
String name = "baeldung";
Optional<String> opt = Optional.ofNullable(name);
```

## 3.用 isPresent() 检查是否为空

在*ifPresent*API出现之前，我们这样检查非空：

```java
if(name != null){
    System.out.println(name.length);
}
```

这个代码在执行之前会检查name是否为空。不仅仅是这种方法比较冗长，而且它还会有很多潜在的bug。

当我们习惯用这种方法时，很容易忘记在代码的某些部分使用null的检查。

这会导致运行时产生*NullPointerException*.当程序因为输入而失败时，这通常是不好的编程实践

Optional通过强制的方法明确处理空值，这是一种良好的编程实践。让我们看一下如何用Optional来重构上面的java代码。

在典型的函数式编程风格中，我们可以对实际存在的对象执行操作：

```java
Optional<String> opt = Optional.of("baeldung");
opt.ifPresent(name -> System.out.println(name.length()));
```

## 5.用 orElse() 设置默认值

当包装在Optional中的值为空时，将使用orElse中值表示。

```java
String nullName = null;
String name = Optional.ofNullable(nullName).orElse("john");
```

## 6.用 orElseGet() 设置默认方法

*orElseGet()*和*orElse()*很像，不同的是orElseGet中需要提供一个supplier类型的函数式接口：

```java
String nullName = null;
String name = Optional.ofNullable(nullName).orElseGet(() -> "john");
```

## 7.orElse 和 orElseGet 的不同点

这两个函数功能机会一样，区别就是一个传的是结果，另一个传的是函数式接口（Lambda）。

考虑一种情况：

```java
String doSomething(){
    //do something
}
```

```java
String name1 = Optional.ofNullable("lyr").orElse(doSomething());
String name2 = Optional.ofNullable("lyr").orElseGet(()->doSomething());
```

这样的话，执行name1要比name2快的多，因为无论Optional中的值是否为null，name1中的doSomething()都会执行。而name2中，只是传入了一个lambda并不会执行。

所以在大多数时候我都更推荐使用orElseGet()，除非值直接传一个不需要计算就可以得出的值，例如第5节中的例子

## 8.用 orElseThrow() 处理异常

当Optional包裹的值为null时，用*orElseThrow()*不会返回默认值，而是抛出一个你设置的异常。

```java
String nullName = null;
String name = Optional.ofNullable(nullName).orElseThrow(IllegalArgumentException::new);
```

## 9.用 get() 得到Optional包裹的值

```java
Optional<String> opt = Optional.of("baeldung");
String name = opt.get();
```

但是，与上面的三种方法不同，*get()*API只能在包裹的对象不为null时返回值。否则会抛出没有此类元素的异常。

这是*get()*API的缺陷，在理想情况下，Optional应该帮助我们避免这个无法预料的异常。因此这个方法违反了Optional的目标，所以可能会在将来的摸个版本中被弃用。

所以建议使用其他变体来处理null的情况。

## 10.未完待续...