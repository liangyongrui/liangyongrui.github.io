---
title: Mysql中交换行操作
date: 2018-03-20 17:00:00
link: 180320-1
tags: mysql
---
#

## leetcode的一道题目

参考：[Swap Salary](https://leetcode.com/problems/swap-salary/description/)

给你一个表

| id | name | sex | salary |
|:--:|:----:|:---:|:------:|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |

把sex的m和f交换，只能用一次update

## 答案

```SQL
UPDATE salary SET sex = CHAR(ASCII('f') ^ ASCII('m') ^ ASCII(sex));
UPDATE salary SET sex = CHAR(ASCII('f') + ASCII('m') - ASCII(sex));
UPDATE salary SET sex = IF(sex='m', 'f', 'm');
```

## 总结：

### 主要是之前不了解MySql的相关用法

#### CHAR(n,...)

返回由参数n,...对应的ascii代码字符组成的一个字串(参数是n,...是数字序列,null值被跳过)

```SQL
char(77,121,83,81,'76') => 'mysql'
char(77,77.3,'77.3') => 'mmm'
```

#### ASCII(str)

返回字符串str的第一个字符的ascii值(str是空串时返回0)

```SQL
ASCII(2) => 50
ascii('dete') => 100
```

#### IF(exPR1,expr2,expr3)

如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2; 否则返回值则为 expr3。IF() 的返回值为数字值或字符串值，具体情况视其所在语境而定。

```SQL
IF(1>2,2,3) => 3
```
