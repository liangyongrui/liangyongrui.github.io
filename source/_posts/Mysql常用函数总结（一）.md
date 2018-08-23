---
title: Mysql常用函数总结（一）
date: 2018-03-21 16:40:51
link: 180321
tags: mysql
---
#

遇到什么总结什么

## DATE_SUB(date,INTERVAL expr type)

date 参数是合法的日期表达式。expr 参数是您希望添加的时间间隔。

|常用的Type 值|
|:-:|
|MICROSECOND|
|SECOND|
|MINUTE|
|HOUR|
|DAY|
|WEEK|
|MONTH|
|QUARTER|
|YEAR|

例子

假设我们有如下的表：

|OrderId|ProductName|OrderDate|
|:-:|:-:|:-:|
|1|'Computer'|2008-12-29 16:25:46.635|

现在，我们希望从 "OrderDate" 减去 2 天。我们使用下面的 SELECT 语句：

```SQL
SELECT OrderId,DATE_SUB(OrderDate,INTERVAL 2 DAY) AS OrderPayDate FROM Orders
```

结果：

|OrderId|OrderDate|
|:-:|:-:|
|1|2008-12-27 16:25:46.635|

## DATEDIFF() 函数返回两个日期之间的天数。

语法DATEDIFF(date1,date2)

date1 和 date2 参数是合法的日期或日期/时间表达式。

例子

```SQL
SELECT DATEDIFF('2008-12-30','2008-12-29') AS DiffDate
```

结果是1

## TO_DAYS() 返回从年份0开始的天数

语法 TODAYS('1997-10-07')

上面返回 729669

## coalesce(n,...) 返回第一个非空表达式的值

例子

```SQL
COALESCE(NULL,NULL,3,4,5)
```

返回3

## ifnull(a,b)

如果a is null 那就返回b