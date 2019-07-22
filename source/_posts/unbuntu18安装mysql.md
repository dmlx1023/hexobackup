---
title: unbuntu18安装mysql
date: 2019-06-10 15:59:40
tags: mysql
---



1,安装mysql

```bash
1, sudo apt-get install mysql-server

2, sudo apt-get install mysql-client
3, sudo apt-get install libmysqlclient-dev
```

2,mysql出现ERROR1698(28000):Access denied for user root@localhost错误解决方法

```bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
#[mysqld]这一块中加入skip-grant-tables
service mysql restart
mysql -u root -p #无需密码登录
use mysql;
update user set authentication_string=password("你的密码"),plugin='mysql_native_password' where user='root';
flush privileges;
#重新进入到mysqld.cnf文件中去把刚开始加的skip-grant-tables这条语句给注释掉。
service mysql restart
mysql -u root -paaa#密码aaa登录
```

3,出现远程连接不上的问题

``` bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
#将bind_address 改为 0.0.0.0
```

4,mysql 问题之 ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password)

``` bash
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
#[mysqld]这一块中加入skip-grant-tables
service mysql restart
mysql -u root -p #无需密码登录
use mysql;
update user set authentication_string=password("你的密码"),plugin='mysql_native_password' where user='root';
flush privileges;
#重新进入到mysqld.cnf文件中去把刚开始加的skip-grant-tables这条语句给注释掉。
service mysql restart
mysql -u root -paaa#密码aaa登录
```

5,mysql常用命令

``` bash
#查询所有数据库
show databases;
#创建数据库
create database demo;
#进入数据库
use demo;
#展示所有表
show tables;
#查询表结构
desc user;
#查询表ddl
show create table user;
```
