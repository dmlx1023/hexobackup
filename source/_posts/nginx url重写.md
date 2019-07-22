---
title: nginx url重写
url: 187.html
id: 187
categories:
  - 技术闲谈
date: 2018-07-03 07:28:34
tags:
---

使用rewrite模块进行url的改写
-------------------

    rewrite   <regex>   <replacement>    [flag];
    
    关键字    正则      替代内容     flag标记
    
    
    
    关键字：其中关键字 不能改变(此关键字必须有)
    正则：perl兼容正则表达式语句进行规则匹配
    
    替代内容：将正则匹配的内容替换成replacement
    
    flag标记：rewrite支持的flag标记 使用$+数字配置正则组的内容，从1开始。 如 $1代表第一处正则匹配的内容
    
    flag标记说明：
    
    last     #本条规则匹配完成后，继续向下匹配新的location URI规则
    
    break     #本条规则匹配完成即终止，不再匹配后面的任何规则
    
    redirect   #返回302临时重定向，浏览器地址会显示跳转后的URL地址
    
    permanent #返回301永久重定向，浏览器地址栏会显示跳转后的URL地址
    

例子

    location ~ (^/bmp/*)(.*)\.(jpg|gif|htm|html|png|js|css|exe|json|ioc|APK|zip)$ {
                        root D:/git/vx-merchant-pc/;
                        rewrite ^/bmp/(.*)$ /$1 break;
                expires 0s;
                    }
    

这是将对bmp的请求转发到我本地的工程里面去，会在root下面直接寻找 bmp后面的路径，其中 $1 代表正则匹配的内容， 1代表第一处。

    if ( $host != 'www.zycat.top'  ) {
    
                    rewrite ^/(.*) http://www.zycat.top/$1 permanent;
    
            }
     }
    

把对这个服务器的所有请求都转发到http://www.zycat.top/ 下面