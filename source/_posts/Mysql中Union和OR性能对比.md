---
title: Mysql中Union和OR性能对比
date: 2018-03-20 16:40:51
link: 180320
tags: mysql
---

参考：[Union and OR and the Explanation](https://leetcode.com/problems/big-countries/discuss/103561/Union-and-OR-and-the-Explanation)

## 看个问题

有两种SQL，结果是一样的，但是第二种快一点：

```SQL
#用OR
SELECT name, population, area
FROM World
WHERE area > 3000000 OR population > 25000000

#用Union
SELECT name, population, area
FROM World
WHERE area > 3000000
UNION
SELECT name, population, area
FROM World
WHERE population > 25000000
```

## 为什么Union比OR更快呢？

### 严格的讲，用Union更快仅限于扫描两个不同的列

(当然，UNION ALL 比 UNION 更快）

### MySQL 通常是使用一个索引来扫描表的，如果有两个索引的时候，第二个索引就要再扫一边

如果时用UNION就是两次都扫一个索引。

（我觉得第一个时间复杂度时O(logn\*logn), 第二个是O(logn\*2), 忽略常数时间复杂度不变)

### benchmark 关于 UNION 和 OR, 有下表

Scenario 3: Selecting all columns for different fields

| | CPU | Reads| Duration | Row Counts |
|:-:|:-:|:-:|:-:|:-:|
| OR | 47 | 1278 | 443 | 1228 |
| UNION | 31 | 1334 | 400 | 1228 |

Scenario 4: Selecting Clustered index columns for different fields

| | CPU | Reads| Duration | Row Counts |
|:-:| :-: | :-: | :-: | :-: |
|OR|  0|  319|     366|  1228|
|UNION | 0  |  50 |  193  |   1228|

### 总结：

1. 从benchmark的表上来看不加索引的话几乎没区别, 甚至用OR的读性能更好。
2. 我觉得在性能不是特别敏感的时候，还是用or比较好。因为代码简介，可读性高。反之用Union
