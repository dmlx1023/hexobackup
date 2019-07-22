---
title: Anaconda 镜像与环境
url: 172.html
id: 172
categories:
  - 编程语言
date: 2018-06-10 23:10:57
tags:
---

安装
--

*   现在 **Anacondad**的完整包

> [下载链接](https://pan.baidu.com/s/1tKCryt8OIGUNk-12Ay_yRw) 密码：ba0q

设置环境变量，在安装的时候选择了自动添加但是没有成功。
---------------------------

*   进入系统环境变量设置，在系统变量-Path下面添加

    D:\Users\duanm\Anaconda3；
    D:\Users\duanm\Anaconda3\Scripts
    

设置 镜像为 清华大学的，不然实在是太慢，各种报错
-------------------------

    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --set show_channel_urls yes
    

*   查看配置
    
    > conda config --show
    
*   使用 conda安装控件
    
    > conda install scrapy