---
title: RocketMqQ入门
url: 44.html
id: 44
categories:
  - 技术闲谈
date: 2018-03-11 11:57:46
tags:
---

RocketMQ入门
==========

@(mq)\[消息队列|入门|MarkDown\]

### 什么是RocketMQ

RcoketMQ是阿里巴巴开源的分布式消息中间件，现已捐赠给Apache基金会。

### RocketMQ特点

*   具有高性能、高可靠、高实时、分布式特点。
*   Producer、Consumer、队列都可以分布式。
*   Producer向一些队列轮流发送消息，队列集合称为Topic，Consumer如果做广播消费，则一个consumer实例消费这个Topic对- - 应的所有队列，如果做集群消费，则多个Consumer实例平均消费这个topic对应的队列集合。
*   能够保证严格的消息顺序
*   提供丰富的消息拉取模式
*   高效的订阅者水平扩展能力
*   实时的消息订阅机制
*   亿级消息堆积能力
*   较少的依赖

### rocketmq的整体架构

![can not find this img!](\rocketmq.png)

### RocketMQ组件描述

**nameserver**

> 提供broker的路由服务，在实例挂掉之后保证集群的可用性。

**producer**

> 消息的生产者，可以用多个具有相同group的producer组成集群。

**broker**

> 队列的实际的执行者，分为master和slave，master具有读写权限borkerid=0，slave只有读权限brokerid>0，使用相同的brokername标记同一组broker。

**consumer**

> 消息的消费者，可以用多个具有相同group的consumer组成集群。

### RocketMQ部署模式

**双master模式**

> 多台master组成集群,当其中一台master停止之后此broker信息不可用

**主从异步模式**

> master和slave异步同步信息，producer发送信息至master成功后返回成功，当master挂掉之后会丢失部分信息。

**主从同步模式**

> slave同步写入master的信息，producer发送消息至master后只有slave也写入成功之后才会返回成功。挂掉一台之后服务无影响。

### FAQ

*   rocket支持消息过滤，消息的定时，批量发送消息。
*   同一个jvm只存在不存在2个相同groupname的producer和consumer
*   rocketmq的消息只会存在一定时间，然后删除，默认3天。
*   一个消息被失败消费一定次数之后就不会被抛弃。
*   rocketmq保证消息至少被消费一次，不保证重复消费。