---
title: mysql索引23事
date: 2019-06-11 13:26:23
tags: mysql
---

## 使用索引的几个注意事项（注意，此篇文章仅针对innodb引擎，mysql版本 5.7.26-0ubuntu0.18.04.1）

1. 独立的列，即索引不能是表达式的一部分，也不能是函数的参数。

2. 最左匹配原则，即索引项需要从最左边开始，例如

   > select * from temp where name like 'li%'
   
   对于联合索引依然遵循这个规则，例如建立联合索引a,b,c
   
   > alter table temp add index (a,b,c) 
   
   则索引将命中 a;ab;a;abc;而无法名字非a开头的查询语句
   
3. 索引选择性，即不重复的索引项k和数据总条数的比值 t，范围在(1/t-1)，越接近于1代表索引区分度越大。对于区分度不够大的索引，mysql server可能选择全表扫描而不走索引，这个区分度可能是（20%也可能是30%）,由此在建立联合索引时，也建议将区分度大的索引排在前面。

4. 索引失效的几种情况

   > 1. 网上许多文章都说如果 索引为null时，查询 select * from temp where name is null,将不走索引，这是错误的，如果查询列包含索引则会走，参考[官方文档](<https://dev.mysql.com/doc/refman/8.0/en/is-null-optimization.html>)  。
   > 2. 当索引项是字符串时，此时查询条件未加引号的情况。 如 name=123不走索引，name='123'就好走索引。
   > 3. explain select * from uum_employee where MOBILENUM !='123213' 这种情况不走索引。
   > 4. 联合索引是否生效和where条件的顺序并没有关系。
   > 5. not in  ,is not null不会走索引
   > 6. or条件如果有一项没有索引则都不会走索引，除非or的每一项都加上索引。
   > 7. 如果mysql使用全表扫描要比使用索引快,则不会使用到索引，这是因为mysql普通索引时二级索引，需要先找二级索引，然后关联到主键（聚簇索引），然后再回表查询数据，有时候可能直接全表扫描会更快点。

## 索引的数据结构

innodb使用B+Tree来组织索引数据，有以下几个优势：

	1. 具有更高的查找效率，O(logn)
 	2. 对于连续查找具有更高的效率，B+树的叶子节点之间由指针相连为有序的链表。
 	3. 对于硬盘的预读机制这种连续数据具有更高的利用率。



## innodb和mysiam的区别



> innodb支持事务
>
> innodb不仅支持表锁也支持行级锁，对于高并发下的细粒度锁具有更高的效率。mysiam只支持表锁
>
> mysiam不支持全文索引
>
> innodb支持外键
>
> mysiam积累的表的count(*)数据