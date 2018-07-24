---
title: spring入门-bean的配置
url: 33.html
id: 33
categories:
  - spring基础
date: 2018-02-04 15:49:45
tags:
---

spring 使用bean的简单例子 .note-content {font-family: 'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;}

*   [spring 使用bean的简单例子](#spring-使用bean的简单例子)
    *   [建立简单的实现类](#建立简单的实现类)
    *   [XML文件配置](#xml文件配置)
    *   [测试类](#测试类)

建立简单的实现类
--------

    package com.csii.ifp.action;
    
    public class HelloWorld {
        public String sayHello(){
            return "Hello World!";
        }
    }
    

XML文件配置
-------

        <bean id="testAction" class="com.csii.ifp.action.HelloWorld"></bean>
    

测试类
---

    package com.csii.ifp.action;
    
    import java.lang.reflect.Modifier;
    
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    
    public class ManTest {
    public static void main(String[] args) {
        @SuppressWarnings("resource")
        ApplicationContext context=new ClassPathXmlApplicationContext("config/springBean/actionBeans.xml");
        HelloWorld helloWorld=(HelloWorld)context.getBean("testAction");
        System.out.println(helloWorld.sayHello());
    
    }
    }
    

输出 **Hello World!**  
这是一个使用spring来进行配置开发的简单例子。spring的优势在于，我们可以将HelloWorld实现某个接口，这样在实现类里面使用接口向上转型，对于不同的实现来说，只需要改变**xml**文件中的bean配置即可，这样实现了代码的接口，也体现的java提倡的**面向接口编程**。