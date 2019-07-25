---
title: 加密hexo文章
date: 2019-07-26 02:25:54
tags:
	- hexo

password: 133272
---

## 安装

> npm install --save hexo-blog-encrypt

## 使用

主配置文件<span style="color:#42b983;font-weight：600">_config.yml</span> 添加配置
```yaml
encrypt:
    enable: true
    default_abstract: 这是一篇加密文章，内容可能涉及个人情感宣泄或私密内容。
    default_message: 请输入密码，查看文章。
```

文章头部配置,会覆盖默认配置。

```yaml
password: 12321323
abstract: 这是一篇加密文章，内容可能涉及个人情感宣泄或私密内容。
message: 请输入密码，查看文章。
```

