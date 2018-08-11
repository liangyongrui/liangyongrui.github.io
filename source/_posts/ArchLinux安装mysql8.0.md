---
title: ArchLinux安装mysql 8.0
date: 2018-08-11 18:42:51
link: 180811
tags: [linux, mysql]
---

## 安装mysql
直接用命令
```shell
sudo pacman -S mysql
```
安装后，log中有这样一段话
```
:: You need to initialize the MySQL data directory prior to starting
   the service. This can be done with mysqld --initialize command, e.g.:
   mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql
:: Additionally you should secure your MySQL installation using
   mysql_secure_installation command after starting the mysqld service
```
所以我们执行这个，记得删除已经存在的datadir
```shell
sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

然后日志里面有一个临时密码可以用于登录
```
root@localhost: tYgu?b2tN/xk
```
启动服务后 就可以登录了

```shell
sudo systemctl start mysqld
```

## mysql 8.0 修改默认密码

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
```

这里的密码需要包含 数字，字母和符号。

由于mysql8.0更改了默认的密码保存方式，所以一定要加

> WITH mysql_native_password

否则老的客户端登录不上