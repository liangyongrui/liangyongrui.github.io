---
title: Mysql + MyBatis插入后得到自增主键
date: 2018-08-13 16:42:51
link: 180813
tags: [MyBatis, mysql]
---

## 需求

使用MyBatis往MySQL数据库中插入一条记录后，需要返回该条记录的自增主键值。

## 方法

在mapper中指定keyProperty属性，示例如下：

```xml
<insert id="insertAndGetId" useGeneratedKeys="true" keyProperty="id" parameterType="com.liangyongrui.model.User">  
    insert into user(username,password)  
    values(#{username},#{password})  
</insert>
```

如上所示，我们在insert中指定了keyProperty="id"，其中id代表插入的User对象的主键属性。

User.java

```java
public class User {  
    private int id;  
    private String username;  
    private String password;  
    //setter and getter  
}
```

UserDao.java

```java
public interface UserDao {  
    int insertAndGetId(User user);  
}
```

## 测试

```java
User user = new User();  
user.setUsername("xxxx");  
user.setPassword("xxxx");  

System.out.println("插入前主键为：" + user.getId());  
userDao.insertAndGetId(user);//插入操作  
System.out.println("插入后主键为：" + user.getId());  
```

输出：

```shell
插入前主键为：0  
插入后主键为：10
```

## 结论

利用keyProperty直接向转入的模型中写入了想要的值，方便与之关联的表调用
