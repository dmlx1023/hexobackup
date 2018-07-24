---
title: "syntax error near unexpected token `$’do\r”"
url: 167.html
id: 167
categories:
  - 问题解决方案
date: 2018-05-28 21:00:24
tags:
---

windows下打开脚本导致换行符问题
===================

    cat -v fileNmae
    

会发现每行后面跟了一个 **^M**符号 可以用下面的命令进行转换或者通过notePad++之类的工具进行转换

    dos2unix fileName