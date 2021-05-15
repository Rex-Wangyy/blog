---
layout: post
title: '初识Docker(三)'
date: 2021-04-11
author: Rex
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Docker
---

## 数据卷与网络

**Data Volume**，数据卷是从外界挂载在容器内文件系统中的文件或者目录，可以脱离容器的生命周期而独立存在。
容器退出或者删除后，数据卷不收任何影响。数据卷数据不继承与镜像，写时复制机制不会作用于数据卷，不会被docker commit提交。

通过创建容器创建的数据卷会自动挂载到容器中，对于通过 -v参数指定的并已经存在的数据卷也会找到并挂载。
可以多个容器共享一个数据卷，可以实现容器之间的文件数据共享。

**数据卷容器** ，专门存放数据卷的容器，不在把宿主机的目录当做数据卷进行挂载，以免破关docker的统一性，
从数据卷容器进行数据卷的挂载。只是其他容器与数据卷连接的桥梁，并不需要保证数据卷容器必须在运行状态。
其他容器只是利用数据卷容器给出的数据卷信息在docker中找到对应的数据卷。最好使用多个数据卷容器存放管理数据卷。



### 数据卷

#### 创建数据卷

```shell
# 创建数据卷 -v 挂载数据卷，可挂载多个
# 所有对 /html 中的文件的操作都是对数据卷的操作
docker run -d --name <container> -v <path1> -v <path2> <image>:<tag>
# 单独创建数据卷
docker volume create --name <volume>

# eg
docker run -d --name myweb -v /html -v /var/log/nginx nginx
docker volume create --name myvolume
```

<img src="https://rex-wangyy.github.io/Imgs/docker/创建挂载数据卷.png">



#### 查看数据卷信息

```shell
# command
docker inspect <volume>

# eg
docker inspect myvolume
```

<img src="https://rex-wangyy.github.io/Imgs/docker/数据卷详细信息.png">



#### 挂载数据卷

```shell
# 挂载已经存在的数据卷 -v <数据卷名>:<挂载路径>
# 如果原路径已存在，会隐藏原有目录，并用数据卷替换
docker run -d --name <container> -v <volume>:<path> <image>:<tag>
# 将宿主机中已存在的目录作为数据卷挂载到容器中 只读 :ro
docker run -d --name <container> -v <local path>:<container path>:ro <image>:<tag>  

# eg
docker run -d --name mycontainer2 -v myvolume:/html nginx
docker run -d --name mycontainer3 -v /Users/yingyu/Documents/docker_dir/volume:/var/lib/mynginx:ro nginx
```

<img src="https://rex-wangyy.github.io/Imgs/docker/挂载数据卷.png">



#### 查看数据卷列表

```shell
docker volume list
```

<img src="https://rex-wangyy.github.io/Imgs/docker/查看数据卷列表.png">



#### 删除数据卷

```shell
# 单独删除/随容器删除
docker rm -v <container>
docker volume rm <volume>
# 删除无主的数据卷
docker volume prune

# eg
docker rm -v mycontainer2
docker volume rm myvolume
```

<img src="https://rex-wangyy.github.io/Imgs/docker/删除数据卷.png">



### 数据卷容器

#### 使用数据卷容器

```shell
# 创建数据卷容器
docker create --name volumeDataContainer -v /html debian:jessie
# 挂载数据卷容器
# --volumes-from 可以挂载指定数据卷容器中所有的数据卷，挂载路径会使用创建数据卷容器时的指定的挂载目录(路径)
docker run -d --name mycontainer --volumes-from volumeDataContainer nginx
# 导出数据卷
# cf create file  P 绝对路径，默认相对路径
docker run -it --volumes-from volumeDataContainer -v $(pwd):/backup --name exporter --rm debian:jessie /bin/bash tar cfP /backup/backup.tar /data
# 直接导出无交互
docker run -it --volumes-from volumeDataContainer -v $(pwd):/backup --rm ubuntu tar cfP /backup/backup2.tar /html
# 恢复数据卷， x extract 从备份文件中还原文件
docker run -it --volumes-from volumeDataContainer -v $(pwd):/backup --rm ubuntu tar xfP /backup/backup.tar /html
```



### 网络

docker通过Network Namespace的方式为每个容器建立了独立的网络。docker启动是会在宿主机上架设一个名为docker0的虚拟网络，用来连接宿主机与容器。

容器启动时会把独立网络通过 Veth Pair 连接到docker0。当连接到docker0上时，就与其他连接到此的容器属于同一个子网中。容器间通过docker0子网来互相访问。



#### 绑定端口，限制ip

```shell
# 端口绑定 -P 随机寻找可用端口绑定 -p 指定端口
docker run -d -P <image>
docker run -d -p <local port1>:<container port1> -p <local port2>:<container port2> ... <image>
# ip限制 只接受此ip的请求
docker run -d -p <ip>:<local port>:<container port> <image>

# eg
docker run -d -p 80:80 -p 443:443 nginx
docker run -d -p 127.0.0.1:443:443 nginx
```



#### 容器间通信

```shell
# 容器间通信 exec进入容器可用 env 查看相关信息
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=root mysql
docker run -d -p 80:80 -p 443:443 --name mynginx --link mysql nginx

# 连接别名 容器中 env 命令查看信息对应名字
docker run -d -p 80:80 -p 443:443 --name mynginx --link mysql:db nginx
```

