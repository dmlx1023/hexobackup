---
title: oracle账户过期
url: 158.html
id: 158
categories:
  - oracle
date: 2018-04-27 19:28:13
tags:
---

    #SCOTT账户为例
    select username,profile from dba_users where username='SCOTT';
    select * from dba_profiles where profile='DEFAULT' and resource_name='PASSWORD_LIFE_TIME';
    alter profile default limit password_life_time unlimited;　
    alter user SCOTT identified by root