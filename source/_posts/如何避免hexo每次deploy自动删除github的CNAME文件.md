---
title: 如何避免hexo每次deploy自动删除github的CNAME文件
date: 2019-06-05 10:24:41
tags:
---

博客每次使用hexo d之后虽然会自动部署到github page,但是会自动删除CNAME，导致域名跳转不过去。

可以将 *CNAME*放入根目录下面的**source**文件夹里面，然后使用hexo g命令会自动将文件复制到**posts**文件夹里面，然后使用hexo d即可部署。

切记：必须先使用 hexo g进行构建。

