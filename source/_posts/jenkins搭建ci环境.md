---
title: jenkins搭建ci环境
date: 2019-08-21 19:57:59
tags: jenkins cicd
---

## jdk安装

下载安装包[jdk](https://download.java.net/openjdk/jdk8u40/ri/jre_ri-8u40-b25-linux-i586-10_feb_2015.tar.gz) 

```bash
tar -zxvf jdk-8u171-linux-x64.tar.gz -C /usr/local/java
export JAVA_HOME=/usr/local/java/jdk1.8.0_171 
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export  PATH=${JAVA_HOME}/bin:$PATH
source /etc/profile
 java -version
```



## maven安装

下载安装包[apache-maven-3.6.2-bin.tar.gz](http://mirror.bit.edu.cn/apache/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz) 

```bash
sudo tar -zxvf apache-maven-3.5.4-bin.tar.gz -C .
export M2_HOME=/opt/apache-maven-3.5.4
export PATH=$PATH:$M2_HOME/bin
source /etc/profile
mvn -v
```

## jenkins 安装

下载 [jenkins.war](http://mirrors.jenkins.io/war-stable/latest/jenkins.war) 

```bash
nohup java -jar jenkins.war --httpPort=8080 &
```



## 插件安装

要实现简单的打包部署我们主要安装三个插件

1. [Git plugin]( https://github.com/jenkinsci/git-plugin)

   使用默认配置即可

2. [Maven Integration plugin](https://wiki.jenkins.io/display/JENKINS/Maven+Project+Plugin)

   ![6X_OL_8YYMP_2~F5N1GU_DM.png](https://i.loli.net/2019/09/14/CUj3rZf1VgWPp9F.png)

3. [Publish Over SSH](http://wiki.jenkins-ci.org/display/JENKINS/Publish+Over+SSH+Plugin)

   ![QQ截图20190914203129.png](https://i.loli.net/2019/09/14/BjfiJy9LT3lNuvr.png)

   其中remote Directory路径是deploy时的根目录，只能在这个目录下操作，也就是部署时jar包一定会传到这个目录下面的某个目录下面。

   

   

## 部署

正常来说我们应该通过pipline,使用jenkinsfile来写pipline脚本进行多流程部署，这样能够方便的进行版本管理，流程也清晰。但是我们普通使用的话不需要去学习pipline语法，我们通过主页的 new item开始一个部署

![部署1.png](https://i.loli.net/2019/09/14/5w4kriyzUbRFZEI.png)
![部署3.png](https://i.loli.net/2019/09/14/wQEXmivjBUKZkho.png)
![部署2.png](https://i.loli.net/2019/09/14/S2icUbCs3xeF85u.png)
![部署4.png](https://i.loli.net/2019/09/14/O7Gcjzsef8up4kw.png)

其中最主要的就是最后一步deploy,可以部署多个transfer,其中各个参数的作用如下：

Source files: 要上传的部署包所在地址

Remove prefix：需要移除的前缀，就是说对于Source files来说查找路径可能有很长，但是我们并不需要在服务器上创建整个路径，这里我们不需要target文件路径，只需要jar包，因此我们去掉target。

Remote directory：对应前面全局的ssh server中相同的配置，也就是说，需要部署的整个路径为：全局路径+这里配置的路径。

Exec command:在包传到服务器之后需要执行的命令，通常是杀进程，启动服务。

小技巧：我们可以通过pre steps改变打包出来的文件名称，这样就能方便的在部署包上传后知道叫什么名称，然后去启动它，这里注意的是这个执行命令的根路径是在 jenkins的workspace下面，就比如我的这个项目叫做wildfly,那么路径就是/home/dmlx/.jenkins/workspace/wildfly，这里面是从git下面拉下来的代码，我们进入target之后再重命名。

> cd target
> mv *.jar jenkins-aaa.jar
> echo "执行成功================"

post steps是上传完之后的脚本，这里给一个post stopes的例子：

```shell

#!/bin/sh
RESOURCE_NAME=demo-jenkins.jar
 
tpid=`ps -ef|grep $RESOURCE_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
echo 'Stop Process...'
kill -15 $tpid
fi
sleep 5
tpid=`ps -ef|grep $RESOURCE_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
echo 'Kill Process!'
kill -9 $tpid
else
echo 'Stop Success!'
fi
 
tpid=`ps -ef|grep $RESOURCE_NAME|grep -v grep|grep -v kill|awk '{print $2}'`
if [ ${tpid} ]; then
    echo 'App is running.'
else
    echo 'App is NOT running.'
fi
 
rm -f tpid
nohup java -jar ./$RESOURCE_NAME --spring.profiles.active=test &
echo $! > tpid
echo Start Success!

```

## 备注

$! 上个命令执行的进程id

$@ 输入参数的集合

$1 输入的第一个参数

$2 输入的第二个参数

$?  上个参数执行的结果，0表示没有错误，其他有错，有时候可能不准。

$$ 脚本当前的id