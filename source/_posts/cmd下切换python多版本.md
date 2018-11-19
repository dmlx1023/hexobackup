---
title: cmd下切换python多版本
date: 2018-10-08 08:33:39
tags:
        - python
---
- 前提 已经安装了Anaconda,这里是在安装了3的情况下切换2


1. conda create -n python2 python=2 ipykernel  
2. 激活安装的python2,切换的版本仅在当前版本有效,windows下没有生效，后来用git bash 执行source activate python2生效了。
> activate python2
3. 关闭切换的版本
> deactivate

> 使用的时候使用 **python2 hello.py**
