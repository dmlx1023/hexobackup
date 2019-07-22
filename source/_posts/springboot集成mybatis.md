---
title: springboot集成mybatis
url: 183.html
id: 183
categories:
  - springboot
date: 2018-06-26 20:03:55
tags:
---

### 配置pom.xml

        <properties>
            <mybatis-spring-boot>1.2.0</mybatis-spring-boot>
            <mysql-connector>5.1.39</mysql-connector>
        </properties>
    
        <dependencies>
    
            <!-- Spring Boot Web 依赖 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
    
            <!-- Spring Boot Test 依赖 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
    
            <!-- Spring Boot Mybatis 依赖 -->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis-spring-boot}</version>
            </dependency>
    
            <!-- MySQL 连接驱动依赖 -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector}</version>
            </dependency>
    
            <!-- Junit -->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
            </dependency>
    
    

### 在application.properties配置连接信息

    ## 数据源配置
    spring.datasource.url=jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8
    spring.datasource.username=root
    spring.datasource.password=root
    spring.datasource.driver-class-name=com.mysql.jdbc.Driver
    
    ## Mybatis 配置
    mybatis.typeAliasesPackage=com.zycat.springbootmybatisdemo.domain
    mybatis.mapperLocations=classpath:mapper/*.xml
    

### 采用xml配置的方式使用sql

    @Autowired
        private UsrMmenusMapper usrMmenusMapper;
        @Override
        public UsrMmenus queryMenu(String menuid ) {
            return  usrMmenusMapper.selectByPrimaryKey(menuid);
        }
    

### 使用mybatisgenerator自动生成sql

#### 配置pom.xml

注：mysql的版本使用8.1的时候只会自动生成insert语句，环境低版本就好了。

    <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>
                <!--mybatis-generator插件-->
                <plugin>
                    <!--Mybatis-generator插件,用于自动生成Mapper和POJO-->
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-maven-plugin</artifactId>
                    <version>1.3.2</version>
                    <configuration>
                        <!--配置文件的位置-->
                        <configurationFile>src/main/resources/conf/mybatis-generator-config.xml</configurationFile>
                        <verbose>true</verbose>
                        <overwrite>true</overwrite>
                    </configuration>
                    <executions>
                        <execution>
                            <id>Generate MyBatis Artifacts</id>
                            <goals>
                                <goal>generate</goal>
                            </goals>
                        </execution>
                    </executions>
                    <dependencies>
                        <dependency>
                            <groupId>org.mybatis.generator</groupId>
                            <artifactId>mybatis-generator-core</artifactId>
                            <version>1.3.2</version>
                        </dependency>
                        <dependency>
                            <groupId>mysql</groupId>
                            <artifactId>mysql-connector-java</artifactId>
                            <!--mysql 8.1的版本只能生成插入语句-->
                            <version>${mysql-connector}</version>
                        </dependency>
                    </dependencies>
                </plugin>
            </plugins>
        </build>
    

### 配置generatorconfig.xml文件

    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE generatorConfiguration PUBLIC
            "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd" >
    <generatorConfiguration>
    
        <!-- 本地数据库驱动程序jar包的全路径 -->
        <!--<classPathEntry location=""/>-->
    
        <context id="context" targetRuntime="MyBatis3">
            <commentGenerator>
                <property name="suppressAllComments" value="false"/>
                <property name="suppressDate" value="true"/>
            </commentGenerator>
    
            <!-- 数据库的相关配置 -->
            <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://localhost:3306/test?serverTimezone=GMT"
                            userId="root" password="root"/>
    
            <javaTypeResolver>
                <property name="forceBigDecimals" value="false"/>
            </javaTypeResolver>
    
            <!-- 实体类生成的位置 -->
            <javaModelGenerator targetPackage="com.zycat.springbootmybatisdemo.domain" targetProject="src/main/java">
                <property name="enableSubPackages" value="false"/>
                <property name="trimStrings" value="true"/>
            </javaModelGenerator>
    
            <!-- *Mapper.xml 文件的位置 -->
            <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
                <property name="enableSubPackages" value="false"/>
            </sqlMapGenerator>
    
            <!-- Mapper 接口文件的位置 -->
            <javaClientGenerator targetPackage="com.zycat.springbootmybatisdemo.mapper"
                                 targetProject="src/main/java" type="XMLMAPPER">
                <property name="enableSubPackages" value="false"/>
            </javaClientGenerator>
    
            <!-- 相关表的配置 -->
            <table tableName="usr_mmenus" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false"
                   enableUpdateByExample="false" />
        </context>
    </generatorConfiguration>
    

最后，maven执行

> mybatis-generator:generate -e 或者使用idea直接执行mybatis-generator这个plugin也可以。

具体代码见github地址，[点击跳转](https://github.com/limuyan44/spring-boot-mybatis-demo)