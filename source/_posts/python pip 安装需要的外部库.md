---
title: python pip 安装需要的外部库
url: 170.html
id: 170
categories:
  - Python
date: 2018-06-10 19:46:34
tags:
---

安装 pip
------

> 安装pip ，我的是windows，最简单的方式是使用python安装包在安装的时候勾选 pip

使用国内镜像进行安装
----------

*   大部分时间官方的地址很快但毕竟是国内，还是镜像快点，以**Scrapy** 为例，使用豆瓣的镜像就很快，官方的经常超时。。

    pip install Scrapy -i https://pypi.douban.com/simple
    
    

常用的命令
-----

*   查看已安装的模块列表
    
    > pip list
    
*   卸载模块
    
    > pip uninstall Scrapy