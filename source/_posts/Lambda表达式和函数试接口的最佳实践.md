---
title: Lambda表达式和函数试接口的最佳实践
date: 2018-08-25 18:10:00
link: 180825
tags: [java8]
---
#

## 1.概述

本文主要深入研究java 8中的函数式接口和Lambda表达式，并介绍最佳实践。

## 2.使用标准的函数式接口

包java.util.function中的函数是接口已经可以满足大部分的java开发者了。这些函数式接口足够的通用和抽象，使用他们可以简单的接收大部分的lambda表达式。开发者应该在创建自己的函数式接口前了解清楚现有的。

考虑一个接口*Foo*:

```java
@FunctionalInterface
public interface Foo {
    String method(String string);
}
```

然后另外一个类*UseFoo*里面有一个用这个接口做参数的*add()*方法:

```java
public String add(String string, Foo foo) {
    return foo.method(string);
}
```

运行他可以这样写：

```java
Foo foo = parameter -> parameter + " from lambda";
String result = useFoo.add("Message ", foo);
```

进一步的观察你会发现*Foo*接口只有一个方法，并这个这个方法接收一个参数和返回一个结果。其实Java 8 已经提供了这样一个接口*Function<T,R>*

我们用*Function<T,R>*来写的话，可以这样：

```java
public String add(String string, Function<String, String> fn) {
    return fn.apply(string);
}
```

运行他可以这样写

```java
Function<String, String> fn = parameter -> parameter + " from lambda";
String result = useFoo.add("Message ", fn);
```

## 3.使用 @FunctionalInterface 注解

在你的函数式接口上用 @FunctionalInterface 。这个接口看起来是没有作用的，甚至你不加上他你的函数式接口还是能工作。

但是你想象一下，一个大工程中可能有很多个接口，其中有一个你打算作为的函数式接口，但是意外的在这个接口中添加了另一个别的抽象方法。这会导致之前用到这个接口的地方失效。

但是你用了 @FunctionalInterface 这个注解后，如果你破坏了预定义的结构编译器会触发一个错误.

所以，用这样的代码

```java
@FunctionalInterface
public interface Foo {
    String method();
}
```

来代替

```java
public interface Foo {
    String method();
}
```

## 4.不要在函数式接口中过度使用默认方法

函数式接口中使用默认方法是可以的：

```java
@FunctionalInterface
public interface Foo {
    String method();
    default void defaultMethod() {}
}
```

函数式接口也可以继承函数式接口，只要他们的抽象方法拥有同样的标识(signature).例如：

```java
@FunctionalInterface
public interface FooExtended extends Baz, Bar {}

@FunctionalInterface
public interface Baz {  
    String method();
    default void defaultBaz() {}
}

@FunctionalInterface
public interface Bar {  
    String method();
    default void defaultBar() {}
}
```

但是像所有的普通接口一样，扩展不同的函数式接口也会有默认方法冲突的问题。例如*Bar*和*Baz*有相同的默认方法*defaultCommon()*,然后你就会得到一个编译错误

```txt
interface Foo inherits unrelated defaults for defaultCommon() from types Baz and Bar...
```

为了解决这个问题，*deaultCommon()*方法应该在Foo接口中覆盖。当然，你可以自定义方法的实现就。但是如果你想用任意一个父接口（例如*Baz*）的默认方法，你需要这样写

```java
Baz.super.defaultCommon();
```

添加太多的默认方法不是一个好的设计方案。为了能够保证接口的向后兼容性，你应该在只有必要的时候才使用默认方法。

## 5.使用Lambda表达式来实例化函数试接口

编译器允许你使用内部类来实例化函数式接口，但是这样会让你的代码变得冗长。所以用lambda表达式更好：

```java
Foo foo = parameter -> parameter + " from Foo";
```

用内部类：

```java
Foo fooByIC = new Foo() {
    @Override
    public String method(String string) {
        return string + " from Foo";
    }
};
```

Lambda表达式可以适用于很多旧库的接口，但是这不意味着适用于全部接口。

## 6.避免使用函数式接口作为重载函数的参数

使用不同的方法名来避免冲突，看个例子：

```java
public interface Adder {
    String add(Function<String, String> f);
    void add(Consumer<Integer> f);
}

public class AdderImpl implements Adder {

    @Override
    public  String add(Function<String, String> f) {
        return f.apply("Something ");
    }

    @Override
    public void add(Consumer<Integer> f) {}
}
```

但是当你尝试运行任何*AddrImpl*的方法时：

```java
String r = adderImpl.add(a -> a + " from lambda");
```

你将会得到错误信息：

```java
reference to add is ambiguous both method
add(java.util.function.Function<java.lang.String,java.lang.String>)
in fiandlambdas.AdderImpl and method
add(java.util.function.Consumer<java.lang.Integer>)
in fiandlambdas.AdderImpl match
```

为了解决这个问题，有两个办法。首先是改方法的名字：

```java
String addWithFunction(Function<String, String> f);

void addWithConsumer(Consumer<Integer> f);
```

其次是类型转化，但是不推荐这样：

```java
String r = Adder.add((Function) a -> a + " from lambda");
```

## 7.不要将lambda表达式视为内部类

尽管在之前的例子里，我们都是把Lambda表达式作为内部类来代替的。但是这两个概念有个重要的不同：范围（scope）

在内部类中，他会创建一个新的范围，你可以在内部的范围中通过创建相同名称的变量来覆盖本地变量。你还可以用this关键字来引用内部类的实例。

但是在lambda表达式中，你不能覆盖外部的变量，并且你用this引用的是类的属性，而不是lambda的。
例如：

```java
private String value = "Enclosing scope value";

public String scopeExperiment() {
    Foo fooIC = new Foo() {
        String value = "Inner class value";

        @Override
        public String method(String string) {
            return this.value;
        }
    };
    String resultIC = fooIC.method("");

    Foo fooLambda = parameter -> {
        String value = "Lambda value";
        return this.value;
    };
    String resultLambda = fooLambda.method("");

    return "Results: resultIC = " + resultIC + ", resultLambda = " + resultLambda;
    }
```

如果你运行*scopeExperiment()*方法将得到

```txt
Results: resultIC = Inner class value, resultLambda = Enclosing scope value
```

## 8.保持lambda表达式短且不言自明（Self-explanatory）

如果可以的话，请使用一行结构，而不是一大块代码。Lambda应该是一种表达式，而不是一种叙述。Lambda应该精准的表达他提供的功能。

这只是风格建议，这并不会影响性能。但是一般而言这样更容易理解和使用。

这可以通过多钟方式实现，让我们仔细看看。

### 8.1 避免Lambda体内使用代码块

在理想的情况下，Lambda应该被写成一行。这样Lambda是一个不言自明的结构。在有参数的情况下，它声明应该用什么数据执行什么操作。

如果有一大块代码，Lambda的功能就不是那么清晰了。

按照这样的思想，应该使用下面的例子

```java
Foo foo = parameter -> buildString(parameter);
```

```java
private String buildString(String parameter) {
    String result = "Something " + parameter;
    //many lines of code
    return result;
}
```

而不是

```java
Foo foo = parameter -> { String result = "Something " + parameter;
    //many lines of code
    return result;
};
```

### 8.2避免指定参数类型

大多数情况编译器是可以自动推断参数类型的。

这样做

```java
(a, b) -> a.toLowerCase() + b.toLowerCase();
```

而不是

```java
(String a, String b) -> a.toLowerCase() + b.toLowerCase();
```

### 8.3避免在单个参数周围使用括号

Lambda表达式的语法只需要在多个参数和没有参数的时候使用括号。

这样做

```java
a -> a.toLowerCase();
```

而不是

```java
(a) -> a.toLowerCase();
```

### 8.4避免使用return表达式和大括号

在一行的Lambda中，return和大括号是可选的。

这样做

```java
a -> a.toLowerCase();
```

而不是

```java
a -> {return a.toLowerCase()};
```

### 8.5使用方法引用

通常（例如前面的例子）Lambda表达式都只是调用其他地方实现的方法。这种情况下使用Java 8的另一个功能：方法引用。

所以lambda

```java
a -> a.toLowerCase();
```

可以被这样代替：

```java
String::toLowerCase;
```

这样不一定总是更短，但是可以增强代码的可读性

## 9.修改对象的变量

lambdas的主要用途之一是用于并行计算 - 这意味着它们在线程安全方面非常有用。

如果非要改变对象变量的值，可以这样

```java
int[] total = new int[1];
Runnable r = () -> total[0]++;
r.run();
```

保留此示例作为提醒，以避免可能导致意外突变的代码。

## 10.结论

在本文中，我们看到了Java 8的lambda表达式和函数式接口中的一些最佳实践和缺陷。尽管这些新功能具有实用性和强大功能，但它们只是工具。每个开发人员在使用它们时都应该注意