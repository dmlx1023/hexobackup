---
title: linux端口与网络查看
url: 150.html
id: 150
categories:
  - linux
date: 2018-04-24 18:55:10
tags:
---

lsof -i:8080
------------

列出使用8080端口的连接

netstat -anp |grep 8080
-----------------------

查看端口的状态