---
title: nginx开启http2.0
url: 120.html
id: 120
categories:
  - 网站相关
date: 2018-04-10 03:02:59
tags:
---

配合ssl使用 listen 443 ssl http2 default_server; 端口用443，发现用了其他端口没走http2.0. nginx重启，访问网站，然后访问chrome的 【chrome://net-internals/#http2】查看http sessions表中是否有配置了2.0的主机地址