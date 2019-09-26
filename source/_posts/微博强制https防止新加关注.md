---
title: 微博强制https防止新加关注
date: 2019-09-20 17:26:12
tags: 小技巧
---

​	起因在于我会用pc版刷微博，最近发现每次打开pc版都会给我新加关注，删除之后发现再次打开还是新加了，此时猜测十有八九cookie被劫持了，虽然微博主站基本https了，但是有些接口还是http的，设置微博强制https一个星期未发现此问题再次发生，故应该是这个原因。

> 打开 [chrome://net-internals/#hsts](chrome://net-internals/#hsts)
>
> 在 Add HSTS domain 添加 weibo.com域名
>
>  Add HSTS domain能查询到即可 