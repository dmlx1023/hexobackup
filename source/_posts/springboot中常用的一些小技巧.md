---
title: springboot中常用的一些小技巧
date: 2019-09-09 16:27:42
tags: springboot
---

## ControllerAdvice/RestControllerAdvice

作为特殊的组件对controller进行拦截处理，RestControllerAdvice代表返回格式默认为json,其中包含了三个功能



1. 异常处理@ExceptionHandler

   ```java
   
       @ExceptionHandler(IllegalArgumentException.class)
       private String businessErrorHandler(IllegalArgumentException e) {
           return "错误:"+e.getMessage();
       }
   ```

2. 对controller的参数格式化@InitBinder

``` java
@InitBinder
    public void globalInitBinder(WebDataBinder binder) {
        binder.addCustomFormatter(new DateFormatter("yyyy-MM-dd"));
    }
```

3.  @ModelAttribute 定义熟悉可以在后续的流程中使用相同注解来获取值

   ``` java
    @ModelAttribute(value = "info")
       public String globalModelAttribute() {
           return "user hhhhhhhhhh";
       }
   ```

   在controller中通过相同注解可以获取到info的值

   ``` java
    @RequestMapping("hello/{nonce}")
       public String hello(User user, @PathVariable String nonce, @ModelAttribute("info") String info) {
           return JSON.toJSONString(user).concat(":=:".concat(nonce).concat(":").concat(info));
   
       }
   ```

   

## HandlerMethodArgumentResolver

对controller的参数进行处理，可以通过这种方法来为controller的参数自动注入值，例如常用的对controller自定注入当前用户方便业务处理。

先定义一个HandlerMethodArgumentResolver的实现

``` java
@Configuration
public class CurrentUserMethodArgumentResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return User.class.isAssignableFrom(parameter.getParameterType());
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
//        HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
        User user = new User();
        user.setId("1111111");
        user.setNickName("dmlx");
        return user;
    }
}
```

其中**supportsParameter**代表是否进行参数处理，这里我们定义了一个**user**用户模型,如果参数是user的子类就进行处理。我们在**resolveArgument**中返回了我们处理过的user,这里要说一下springmvc的处理流程：

> ```
> HandlerInterceptor--> HandlerMethodArgumentResolver--> Aspect注解的aop实现
> ```

因为我们接下来会使用**HandlerInterceptor**进行用户登录的判断。



## HandlerInterceptor

如同名字所说，这是springmvc中对handler的拦截器，其中包含了三个方法

- preHandle：在业务处理器处理请求之前被调用。预处理，可以进行编码、安全控制、权限校验等处理，返回**True**才会继续往下执行。

- postHandle：在业务处理器处理请求执行完成后，生成视图之前执行。后处理（调用了Service并返回ModelAndView，但未进行页面渲染），有机会修改ModelAndView；

- afterCompletion：在DispatcherServlet完全处理完请求后被调用，可用于清理资源等。返回处理（已经渲染了页面）

  ### 执行顺序

  多个拦截器通过**order**设置执行顺序值越小先执行，执行preHandle按正常顺序执行，而postHandle，afterCompletion则按相反顺序执行。

  >  ==========我是第一个拦截器的preHanlde
  > ==========我是第二个拦截器的preHanlde
  >  ==========我是第二个拦截器的postHanlde
  > ==========我是第一个拦截器的postHandle
  > ==========我是第二个拦截器的afterCompletion
  > ==========我是第一个拦截器的afterCompletion

### springboot配置

```java
import java.util.List;
@Configuration
public class Config implements WebMvcConfigurer {
    @Autowired
    private DemoHandlerInterceptor demoHandlerInterceptor;
    @Autowired
    private Demo02HandlerInterceptor demo02HandlerInterceptor;
    @Autowired
    private CurrentUserMethodArgumentResolver currentUserMethodArgumentResolver;
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(currentUserMethodArgumentResolver);
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(demoHandlerInterceptor).excludePathPatterns("/login").order(1);
        registry.addInterceptor(demo02HandlerInterceptor).excludePathPatterns("/login").order(2);
        /**
         - /**： 匹配所有路径
         - /admin/**：匹配 /admin/ 下的所有路径
         - /secure/*：只匹配 /secure/user，不匹配 /secure/user/info

         */
    }
}
```

注意，对于以往的springmvc配置我们通常都会通过继承**WebMvcConfigurerAdapter**来配置，因为jdk1.8出现了默认方法，导致spring将配置都移植到了**WebMvcConfigurer**接口当中使用默认方法来提供实现。

