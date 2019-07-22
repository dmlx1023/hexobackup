---
title: windows添加静态路由实现内网网同时连接
url: 125.html
id: 125
categories:
  - 小技巧闲谈
date: 2018-04-12 09:05:46
tags:
---

先举一个栗子

> route add 66.0.0.0 mask 255.0.0.0 66.4.17.14

这是将所有66开头的请求转发到66.4.17.14网关，255.0.0.0是子网掩码 route delete 66.* 可以删除路由 route print 打印路由 route delete 0.0.0.0 这是我的内外网配置

    route delete 0.0.0.0
    #外网
    route add 0.0.0.0 mask 0.0.0.0 10.124.0.1
    #内网
    route add 66.0.0.0 mask 255.0.0.0 66.4.17.14