---
title: 网站使用let’s Encrypt 安装ssl证书
url: 115.html
id: 115
categories:
  - 网站相关
date: 2018-04-10 01:10:15
tags:
---

> 官网上生成证书的方式有很多种，正好ecs上面有git所以就采用了**certbot**来生成

        git clone https://github.com/certbot/certbot
        cd certbot/
        ./certbot-auto certonly --standalone --email duanmu3209211994@163.com -d zycat.top -d www.zycat.top
    
    

执行完上面之后生成的证书在【/etc/letsencrypt/】这里面，如果生成的过程中80端口报错的话是因为nginx占用了。执行【service nginx stop】关掉nginx即可。 我们用到的证书在

> ssl\_certificate /etc/letsencrypt/live/zycat.top/fullchain.pem; ssl\_certificate_key /etc/letsencrypt/live/zycat.top/privkey.pem;

解下来配置和nginx.conf即可 为了方便起见将原来http的server改成

    server{
    #ssl start
            listen 443 ssl;
            ssl on;
            ssl_certificate /etc/letsencrypt/live/zycat.top/fullchain.pem;
             ssl_certificate_key /etc/letsencrypt/live/zycat.top/privkey.pem;
            #ssl end
        #下面还是原来的配置如
        index index.html index.htm index.php;
            root  /home/wwwroot/default;
    }
    

然后再新建一个用来转发的http server,这里主要是为了全站https.

    server
            {
            listen 80 default_server;
            #listen [::]:80 default_server ipv6only=on;
            server_name www.zycat.top;
                return      301 https://$server_name$request_uri;
            }
    

证书有效期问题
-------

官方提供的证书只有三个月的有效期，需要我们手动去重新申请证书。 这个其实也简单，重复上面的生成命令就好了。可以使用linux的定时任务去执行，执行之前最好备份一下原有的证书。不过这里的

    ./certbot-auto certonly --renew-by-default  --email duanmu3209211994@163.com -d zycat.top -d www.zycat.top
    

就不是_standalone_而是_renew-by-default_了。 定时任务,每5天请求一次，当发现证书有效期小于30天的时候就会去请求新的证书，而且定时任务重启机器会重新即使。使用renew-hook会在请求成功证书后重新加载nginx.否则nginx的缓存还是失效的证书。

    0 * */5 * * certbot renew --quiet --renew-hook "/etc/init.d/nginx reload"  
    
    

有时间讲讲linux的定时任务。