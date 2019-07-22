---
title: unbuntu18安装docker
date: 2019-06-10 14:10:20
tags: docker
---



1.更换国内软件源，推荐中国科技大学的源，稳定速度快

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
sudo apt update
```

2.安装需要的包

```bash
sudo apt install apt-transport-https ca-certificates software-properties-common curl
```

3.添加 GPG 密钥，并添加 Docker-ce 软件源，这里还是以中国科技大学的 Docker-ce 源为例

```bash
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
$(lsb_release -cs) stable"
```

4.添加成功后更新软件包缓存

```bash
sudo apt update
```

5.安装 Docker-ce

```bash
sudo apt install docker-ce
```

6.设置开机自启动并启动 Docker-ce（安装成功后默认已设置并启动，可忽略）

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

7.测试运行

```bash
sudo docker run hello-world
```

8.添加当前用户到 docker 用户组，可以不用 sudo 运行 docker

```bash
# sudo groupadd docker  默认已经添加了docker组
sudo usermod -aG docker $USER
#更新用户组
newgrp docke
```

9.测试添加用户组

```bash
docker run hello-world
```

[参考链接](<https://www.runoob.com/docker/ubuntu-docker-install.html>)

