---
title: 使用mdc记录traceid
date: 2019-09-03 14:16:54
tags: 分布式
---

在分布式系统中，日志记录是非常重要的，通过traceid将不同系统的交易联合起来以及快速查找一次请求的处理过程。*slfj*提供了一种实现方式，就是*mdc*,mdc的原理在于使用ThreadLocal/InheritableThreadLocal将一个线程内处理的交易变量存储下来，在日志文件中能够按规范打印出来方便日志查找。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} | %thread | %-5level | %logger |%X{requestId}-%m%n</pattern>
        </encoder>
    </appender>

    <logger name="com.zycat.demomdcspringboot.controller.HelloController" level="debug" />
    <root level="debug">
        <appender-ref ref="console" />
    </root>

</configuration>
```

例如使用**MDC.put("requestId",reuqestId)**此时就可以打印出交易流水，在springboot项目中有很多方式，例如filter,aop,HandlerInterceptor 

```java
@Component
@Order(1)
@WebFilter(urlPatterns = "/*",filterName = "demoFilter")
public class DemoFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        MDC.put("requestId","seq1111111111111111111111111");
        filterChain.doFilter(servletRequest,servletResponse);
    }
}

```

在分布式应用中通过context传输traceid,此时通过logstash之类的采集工具将日志采集到MQ->Elasticsearch,最终通过ES语法能够方便快速的查找一笔交易的整个流程。