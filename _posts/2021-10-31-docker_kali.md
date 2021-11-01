---
layout: post
title: kali上安装docker
categories: Linux
description: Linux
keywords: Linux
---

kali上安装docker和ARL(Asset Reconnaissance Lighthouse)资产侦察灯塔系统

### 1、安装docker

步骤：

```bash

# 1. 更新
$ sudo apt update

# 2. 安装依赖包
$ sudo apt -y install curl gnupg2 apt-transport-https software-properties-common ca-certificates

# 3. 导入用于签署Docker软件包的Docker GPG密钥
$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

# 4.添加包含Docker CE最新稳定版本的Docker存储库
echo "deb [arch=amd64] https://download.docker.com/linux/debian buster stable" | sudo tee  /etc/apt/sources.list.d/docker.list

# 5. 更新apt包索引：
$ sudo apt update

# 6. 要在Kali Linux上安装Docker CE
sudo apt install docker-ce docker-ce-cli containerd.io

# 7. 此安装会将docker组添加到系统中，而无需任何用户，将用户帐户添加到组中，以非特权用户身份运行docker命令：
sudo usermod -aG docker $USER

# 8.检查安装的Docker版本：
$ docker version

# 9. Docker Compose 如果没有的话会提示安装
docker-compose version
```

安装完成可以测试

```bash
docker run --rm -it  hello-world
```

docker基础命令

```bash
# 启动docker
sudo service docker start

# 停止docker
sudo service docker stop

# 重启docker
sudo service docker restart

# 显示正在运行的容器
docker-compose ps

# 停止容器
docker-compose stop
docker-compose rm
```

### 2、安装ARL

Docker 启动:

```bash
git clone https://github.com/TophantTechnology/ARL
cd ARL/docker/
docker volume create arl_db
docker-compose pull
docker-compose up -d 
```

或者直接下载`docker-compose`配置文件启动

```bash
mkdir docker_arl
wget -O docker_arl/docker.zip https://github.com/TophantTechnology/ARL/releases/download/v2.4.1/docker.zip
cd docker_arl
unzip -o docker.zip
docker-compose pull
docker volume create arl_db
docker-compose up -d
```

我这里先直接`git clone https://github.com/TophantTechnology/ARL`

然后cd进去：

```bash
cd ARL/docker

sudo service docker start

sudo docker volume create arl_db

sudo docker-compose pull

sudo docker-compose up -d 
```

都弄完之后可以通过`docker ps -a`查看

应该会有一个`5003`的端口开放

浏览器打开`localhost:5003`，打开登录页。

账号密码：`admin/arlpass`

登录进去就是灯塔系统的界面。

### 3、docker依赖拉取timeout

docker默认源是国外源，所以国内拉取很慢或者直接报错

解决方法：

```bash
sudo su -l root


vi /etc/docker/daemon.json
#添加如下网易镜像源
{
"registry-mirrors": ["http://hub-mirror.c.163.com"]
}


############# 或者
vi /etc/sysconfig/docker
#编辑OPTIONS，添加中国科技大学的镜像源
OPTIONS='--selinux-enabled --log-driver=journald --registry mirror=https://docker.mirrors.ustc.edu.cn'
```

一定得切换root用户，因为docker的配置文件的权限是`rw-r-r`
