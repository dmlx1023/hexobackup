---
title: spring事务那些事儿
date: 2019-06-14 16:19:34
tags:
---

spring事务算是一个非常重要的功能，也是aop的重要应用之一，我们这里不涉及aop的具体实现，谈谈spring事务本身。

在没有spring之前，我们如何通过java本身的api来操作事务的呢，这里写了一个小demo:

``` java
 Class.forName("com.mysql.cj.jdbc.Driver");
        String url = "jdbc:mysql://XX.XX.XX.XX:3306/demo?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC&useSSL=true";
        Connection connection = DriverManager.getConnection(url, "user", "pwd");
        connection.setAutoCommit(false);
        Savepoint savepoint=connection.setSavepoint("checkName");
        PreparedStatement statement = connection.prepareStatement("select * from d ");
        PreparedStatement statement1 = connection.prepareStatement("delete from d wher c=1 ");

        try {
            ResultSet r = statement.executeQuery();
            statement1.execute();
            while (r.next()) {
                System.out.println(r.getRowId(1));
            }
            r.close();
        } catch (Exception e) {
            connection.rollback();
//            connection.rollback(savepoint);
        }
        connection.commit();
        connection.close();
```

从上面例子可以看到，其实对于我们的业务相关的就只是sql执行的几行代码，而数据源连接和关闭，事务回滚与提交的代码其实都是固定的，spring要做的就是抽出共通的代码，只关心业务特殊的部分，这恰恰是<span style="color:red">AOP</span> 所要达到目的。

AOP的原理不细说，无非是JdkPorxy和Cglib。

这里主要关注spring事务提供的几种传播级别，spring中的传播级别由**Propagation* *提供，下面来一一说明这几种之间的差别。

| 传播级别                  | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| REQUIRED                  | 支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择，也是 Spring 默认的事务的传播。 |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果当前没有事务就以非事务方式执行。           |
| PROPAGATION_MANDATORY     | 必须在事务中执行，否则抛出一个异常                           |
| PROPAGATION_REQUIRES_NEW  | 当前没有事务时就开启一个新的事务，如果当前存在事务，则暂停当前的事务，重新开启一个事务，这意味着，新事务拥有单独的提交与回滚而不受外部事务的影响。一旦新事务完成的提交与回滚，外部事务是没有任何办法进行改变了，同样，除非新事务抛出异常导致外部事务的提交与回滚，否则新事务与外部事务没有任何关系。 |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式执行，如果当前存在事务就挂起当前事务。这意味着，当前执行的方法setAutoCommit=True,和事务没有关系。 |
| PROPAGATION_SUPPORTED     | 以非事务方式执行，如果当前存在事务就加入当前事务，需要特殊的事务管理器支持。 |
| NEVER                     | 以非事务方式执行，如果当前存在事务就抛异常。                 |
| NESTED                    | 这个事务稍微复杂一点，属于嵌套事务（只有<span style="color:red">JDBC DataSourceTransactionManager</span>支持），在外部没有事务时就新建一个事务，要理解这个，需要看懂上面的demo,嵌套事务的核心是*SavePoint*,在内部事务执行前设置一个*SavePoint*，这样当内部事务回滚时就会回滚到*SavePoint*处，外部事务的回滚和提交同样会影响内部事务的commit和rollback,而内部事务对外部事务的影响就要取决于外部事务是否捕获内部事务抛出的异常。 |







