---
title: springboot集成swagger
url: 178.html
id: 178
categories:
  - springboot
date: 2018-06-26 19:45:09
tags:
---

pom.xml配置
---------

    <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger2</artifactId>
                <version>2.2.2</version>
            </dependency>
            <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger-ui</artifactId>
                <version>2.2.2</version>
            </dependency>
    
    

配置swagger的主类
------------

    package com.zycat.springbootmybatisdemo;
    
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    import springfox.documentation.builders.ApiInfoBuilder;
    import springfox.documentation.builders.PathSelectors;
    import springfox.documentation.builders.RequestHandlerSelectors;
    import springfox.documentation.service.ApiInfo;
    import springfox.documentation.spi.DocumentationType;
    import springfox.documentation.spring.web.plugins.Docket;
    import springfox.documentation.swagger2.annotations.EnableSwagger2;
    
    /**
     * @Description swagger config
     * @Author duanmulixiang
     * @create 2018-06-25 20:37
     * @Version 1.0
     **/
    @Configuration
    @EnableSwagger2
    public class Swagger2 {
    
        @Bean
        public Docket createRestApi() {
            return new Docket(DocumentationType.SWAGGER_2)
                    .apiInfo(apiInfo())
                    .select()
                    .apis(RequestHandlerSelectors.basePackage("com.zycat.springbootmybatisdemo.controller"))
                    .paths(PathSelectors.any())
                    .build();
        }
    
        private ApiInfo apiInfo() {
            return new ApiInfoBuilder()
                    .title("Spring Boot中使用Swagger2构建RESTful APIs")
                    .description("本项目用于测试swagger作为api文档")
                    .termsOfServiceUrl("http://www.zycat.top/")
                    .contact("端木礼响")
                    .version("1.0")
                    .build();
        }
    
    }
    
    

配置swagger的主类
------------

    package com.zycat.springbootmybatisdemo;
    
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    
    import springfox.documentation.builders.ApiInfoBuilder;
    import springfox.documentation.builders.PathSelectors;
    import springfox.documentation.builders.RequestHandlerSelectors;
    import springfox.documentation.service.ApiInfo;
    import springfox.documentation.spi.DocumentationType;
    import springfox.documentation.spring.web.plugins.Docket;
    import springfox.documentation.swagger2.annotations.EnableSwagger2;
    
    /**
     * @Description swagger config
     * @Author duanmulixiang
     * @create 2018-06-25 20:37
     * @Version 1.0
     **/
    @Configuration
    @EnableSwagger2
    public class Swagger2 {
    
        @Bean
        public Docket createRestApi() {
            return new Docket(DocumentationType.SWAGGER_2)
                    .apiInfo(apiInfo())
                    .select()
                    .apis(RequestHandlerSelectors.basePackage("com.zycat.springbootmybatisdemo.controller"))
                    .paths(PathSelectors.any())
                    .build();
        }
    
        private ApiInfo apiInfo() {
            return new ApiInfoBuilder()
                    .title("Spring Boot中使用Swagger2构建RESTful APIs")
                    .description("本项目用于测试swagger作为api文档")
                    .termsOfServiceUrl("http://www.zycat.top/")
                    .contact("端木礼响")
                    .version("1.0")
                    .build();
        }
    
    }
    
    

### 在启动类中配置的扫描包里面配置swagger的api数据

    @ApiOperation(value = "登录接口",notes = "这是使用用户名和密码登录的接口")
        @ApiImplicitParams({
                @ApiImplicitParam(name = "name",value = "名称",required = true,paramType ="query",dataType = "String"),
                @ApiImplicitParam(name = "pwd",value = "密码",required = true,paramType = "query",dataType ="String")
        })
        @RequestMapping(value = "/login",method = RequestMethod.GET)
        public UsrMmenus login(@RequestParam(value = "name",required = true) String name, @RequestParam(value = "pwd",required = true) String pwd){
            return userService.queryMenu("MActivityApply");
        }
    

### 详细代码已分享到github,[点击访问](https://github.com/limuyan44/swagger-demo)