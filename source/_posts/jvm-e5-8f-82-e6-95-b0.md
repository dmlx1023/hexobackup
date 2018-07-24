---
title: jvm参数
url: 152.html
id: 152
categories:
  - jvm
date: 2018-04-24 21:59:20
tags:
---

    #堆内存最小值
    -Xms20m
    #堆内存最大值，在hotspot中设置最小最大相同时为了防止gc时内存动态扩展
    -Xmx20m
    -Xmn164m
    #永久代的大小，在jdk8里面已经改为metaspace,存储类的元数据，方法区的一种实现
    -XX:MaxPermSize=250m
    #保留代码占用的内存容量，编译后的二进制字节文件最大值
    -XX:ReservedCodeCacheSize=64m
    -Xverify:none
    #关闭类的自动gc,这里指的是方法区的类的定义的回收，不是堆上面的对象的回收。
    -Xnoclassgc
    #指定gc方式
    -XX:+UseParNewGC
    -XX:+UseConcMarkSweepGC
    -XX:CMSInitiatingOccupancyFraction=85
    #线程的堆栈大小，影响方法的数量，会出现StackOverflowError
    -Xss128k
    #开启断言
    -ea
    #打印gc日志
    -XX:+PrintGCDetails