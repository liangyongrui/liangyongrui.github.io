---
title: java中Optional的使用教程
date: 2018-08-26 10:41:04
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

## 10.用 filter() 过滤返回值

经过filter的值会被filter过滤，只留下满足（返回true）给定lambda的值：

```java
Integer year = 2016;
Optional<Integer> yearOptional = Optional.of(year);
boolean is2016 = yearOptional.filter(y -> y == 2016).isPresent(); //true
boolean is2017 = yearOptional.filter(y -> y == 2017).isPresent(); //false
```

这样写有一个好处，就是不需要用多余的代码来判断被包裹的类型是否为null。

再看个例子，我有个检查价格是否在范围内的函数：

```java
public boolean priceIsInRange1(Modem modem) {
    boolean isInRange = false;
    if (modem != null && modem.getPrice() != null
      && (modem.getPrice() >= 10
        && modem.getPrice() <= 15)) {
        isInRange = true;
    }
    return isInRange;
}
```

如果用filter来写，可以这样：

```java
public boolean priceIsInRange2(Modem modem2) {
     return Optional.ofNullable(modem2)
       .map(Modem::getPrice)
       .filter(p -> p >= 10)
       .filter(p -> p <= 15)
       .isPresent();
 }
```

用filter的程序明显更健壮，因为不用担心null忘记处理的问题

## 11.用 map() 转换值

在上一节中，我已经用到了*map()*，map可以把一个值用某个函数（Lambda）处理过，然后用他的返回值代替。再看两个例子：

```java
List<String> companyNames = Arrays.asList(
    "paypal", "oracle", "", "microsoft", "", "apple");
Optional<List<String>> listOptional = Optional.of(companyNames);
int size = listOptional.map(List::size).orElse(0); //6
```

```java
String name = "baeldung";
Optional<String> nameOptional = Optional.of(name);
int len = nameOptional.map(String::length()).orElse(0); //8
```

我们可以用*map*和*filter*一起做更强大的事情。

比如我们要检查用户从前端传回的密码。我们可以这样做：

```java
String password = " password ";
Optional<String> passOpt = Optional.of(password);
boolean correctPassword = passOpt
    .filter(pass -> pass.equals("password"))
    .isPresent(); //false
correctPassword = passOpt
    .map(String::trim)
    .filter(pass -> pass.equals("password"))
    .isPresent(); //true
```

正如你看到的，我们用*map*去掉了password的首尾空格

## 12.用 flatMap() 转化值

*flatMap()*和*map()*类似，都可以转换值，但是他们不同的是*map()*中获取的返回值自动被Optional包装,即返回值 -> Optional<返回值>。*flatMap()*中返回值保持不变,但必须是Optional类型,即Optional<返回值> -> Optional<返回值>。

可以从源码看出

```java
public <U> Optional<U> map(Function<? super T, ? extends U> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent()) {
        return empty();
    } else {
        return Optional.ofNullable(mapper.apply(value));
    }
}

public <U> Optional<U> flatMap(Function<? super T, ? extends Optional<? extends U>> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent()) {
        return empty();
    } else {
        @SuppressWarnings("unchecked")
        Optional<U> r = (Optional<U>) mapper.apply(value);
        return Objects.requireNonNull(r);
    }
}
```

所以可以这样使用他们：

```java
class FlightTicketInfo {

    private String orderNumber;

    public String getOrderNumber() {
        return orderNumber;
    }
}

public class OptionalTest {

    public void testMap() {
        FlightTicketInfo flightTicketInfo = null;

        Optional<Optional<String>> s1 = Optional.ofNullable(flightTicketInfo).map(OptionalTest::getOrderNumber);

        Optional<String> s2 = Optional.ofNullable(flightTicketInfo).map(FlightTicketInfo::getOrderNumber);

        Optional<String> s3 = Optional.ofNullable(flightTicketInfo).flatMap(OptionalTest::getOrderNumber);
    }

    private static Optional<String> getOrderNumber(FlightTicketInfo flightTicketInfo) {
        return Optional.ofNullable(flightTicketInfo).map(FlightTicketInfo::getOrderNumber);
    }
}
```

## 13.java 9中新增的 or() 方法

有时候我们的Optional为空，但是我们又想执行一些其他返回*Optional*的操作。之前在java 8 中，我们有*orElse()*和*orElseGet()*，但是都需要返回未包装的值。

如果我们的*Optional*为空，java 9中的*or()*可以“懒惰（lazily）”的返回包装的值。懒惰就是类似于*orElseGet()*一样，不执行不会计算。

```java
String defaultString = "default";
Optional<String> value = Optional.empty();
Optional<String> defaultValue = Optional.of(defaultString);
Optional<String> result = value.or(() -> defaultValue);
```

result与defaultValue相同

## 14.java 9中新增的 ifPresentOrElse() 方法

我们通常想对Optional的的底层做一些特定的操作，或者如果Optional为空时，我们希望能做另一些事情。
*ifPresentOrElse()*就是为了这种情况诞生的。

先看一源码：

```java
public void ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction) {
    if (value != null) {
        action.accept(value);
    } else {
        emptyAction.run();
    }
}
```

如果Optional中有值则会调用对应的Consumer，如果为空则调用Runnable。

假设我们有一个已定义的Optional，如果值存在，我们想要增加一个特定的计数器：

```java
Optional<String> value = Optional.of("properValue");
AtomicInteger successCounter = new AtomicInteger(0);
AtomicInteger onEmptyOptionalCounter = new AtomicInteger(0);

value.ifPresentOrElse(
    v -> successCounter.incrementAndGet(),
    onEmptyOptionalCounter::incrementAndGet);
```

这样就可以记录成功和失败的个数了。

## 15. java 9中新增的 stream() 方法

添加到Java 9中的Optional类的最后一个方法是*stream()*方法。

Java具有非常流畅和优雅的Stream API，可以对集合进行操作并利用许多函数式编程概念。 在Optional类上引入了*stream()*方法，允许我们将*Optional*实例视为*Stream*。

假设我们有一个已定义的*Optional*，我们正在调用*stream()*方法。 这将创建一个元素的*Stream*，我们可以在其上使用*Stream* API中提供的所有方法：

```java
Optional<String> value = Optional.of("a");
List<String> collect = value.stream().map(String::toUpperCase).collect(Collectors.toList());
```

如果Optional为空，则创建一个空的stream

```java
Optional<String> value = Optional.empty();
List<String> collect = value.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

在空Stream上操作不会有任何影响，但是由于*stream()*方法，我们现在可以使用Stream API链接Optional API。 这使我们能够创建更优雅，更流畅的代码。

## 16.总结

本文简要的介绍了java 8中的Optional和java 9中补充的3个方法。