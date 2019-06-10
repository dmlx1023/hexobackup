---
title: dns解析问题
url: 206.html
id: 206
categories:
  - 技术闲谈
date: 2018-07-18 19:59:18
tags:
---

原由
--

下班的时候生产大面积出现网关系统错，排查之后是请求微信报错，网络情况是 服务-apache代理-f5.

解决
--

发现的情况是访问外网完全不通，找网络方面协助排查，外网网络没有变动，网络状况也是正常的。猜测dns解析出现问题，使用host配置域名，服务正常。后来，排查到服务器dns配置被人在第一条加了一个内网的 用于解析 ntp服务器nameserver。

命令
--

    #查看dns，其中nameserver会按文件内配置顺序来解析，修改此文件不需要重启，有的nameserver超过某个数量之后的server就不会生效。
    cat /etc/resolv.conf
    
    #修改host文件
    cat /etc/host
    # ip 域名
    # 127.0.0.1 wwww.baidu.com