---
title: Mysql查找表中的多组前n大元素
date: 2018-03-22 16:40:51
link: 180322
tags: mysql
---
#

如果时单组很简单，只需要排序后去前n个就行了，但是多组排序似乎就不是那么好做了。

## 解法

**假设需要比较的字段是a，找出前n大的行，则答案为count(比a小且和比较行在同一组的行) < n的行。**（说起来有点绕。。看个例子就懂了）

## 例子

 假设有这样的一个表 Employee

| Id | Name  | Salary | DepartmentId |
|:-:|:-:|:-:|:-:|
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |

找出每个部门薪水的前三名

```SQL
SELECT
  e1.DepartmentId department,
  e1.Name         Employee,
  e1.Salary
FROM
  Employee e1
WHERE
  3 > (SELECT COUNT(DISTINCT e2.Salary)
       FROM
         Employee e2
       WHERE
         e2.Salary > e1.Salary
         AND e1.DepartmentId = e2.DepartmentId
  );
```

结果为

| Department | Employee | Salary |
|:----------:|:--------:|:------:|
| 1          | Joe      | 70000  |
| 2          | Henry    | 80000  |
| 2          | Sam      | 60000  |
| 1          | Max      | 90000  |
| 1          | Randy    | 85000  |

参考资料：[Department Top Three Salaries](https://leetcode.com/problems/department-top-three-salaries/solution/)
