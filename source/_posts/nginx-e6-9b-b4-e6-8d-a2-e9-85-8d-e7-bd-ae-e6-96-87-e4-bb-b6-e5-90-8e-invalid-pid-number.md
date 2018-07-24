---
title: nginx 更换配置文件后 invalid pid number
url: 197.html
id: 197
categories:
  - 问题解决方案
date: 2018-07-16 20:47:40
tags:
---

前言
--

今天做对象存储迁移的时候发现现有的nginx服务器与新的对象存储地址不通，于是便找了一台机器装了nginx做跳转。没想到跳板机ping的通但是端口没开（ping使用的是icmp协议处于网络层，没有端口）,后来发现跳板机上面以前装了个tomcat,开了8080端口，于是便通过8080端口跳到这台机子上然后再转发的新的对象存储地址。

> rpm -ivh nginx.rpm

之后，替换nginx.conf （因为已经有了一套完善的nginx.conf的配置，所以就没有使用原来的。），

> nginx -t

未报错

> nginx -s reload

报错。最初是想

> echo pid号 > xxx.pid 文件

很不幸，**ps -ef|grep nginx** 一片空白。于是，使用

> nginx -c nginx.conf

重新加载配置文件，搞定，ok。