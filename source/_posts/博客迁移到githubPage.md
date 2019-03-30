---
layout: 博客迁移到github page
title: 博客迁移到github page
date: 2019-03-31 04:00:33
tags:
---



之前阿里云的机子到期了，当时为了能够fg买的香港的机器导致小水管对于https来说很慢，然后fg又被检测到了，就想着到期换个大陆的机器。后来发现机器的配置似乎只能搭个小网站，跑服务还得靠本地的机器，所以就没有续费了。最近正好离职了,正好闲着就把博客迁移到了github page上了。

​	教程网上也很多，基本都差不多就不细说了。博主的流程是本来打算用hexo自带的git功能使用*hexo d*自动上传，但是每次都是force update。因为这个也不是什么阻塞的问题可有可无，所以也没有仔细研究，直接在本地将静态页面生成后直接push到远程仓库。

```bash
hexo g
git add .
git commit -m "博客迁移"
git push
```

上面的流程是错误的，应该使用 hexo d，使用之前在 **_config.yml**里面将git地址配成github page地址

```yaml
deploy:
  type: git
  repository: git@github.com:limuyan44/limuyan44.github.io.git
  #repository: git@github.com:limuyan44/hexobackup.git
  branch: master	
  message: update
```



之前的域名由于指向的服务器到期给关了，正好在阿里云的控制台将域名解析到github page的地址。

博客地址 [zycat.top](https://blog.zycat.top/)

 

