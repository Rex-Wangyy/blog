---
layout: post
title: '初识Docker(一)'
date: 2021-04-10
author: Rex
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Docker
---

## Container 容器

容器会包含代码运行时需要的一切，无论环境如何不会对容器内代码造成影响。



#### 创建容器

```shell
# 创建容器返回容器ID
docker create <image>:<tag>
# eg
docker create debian:jessie
```

<img src="https://rex-wangyy.github.io/Imgs/docker/%E5%88%9B%E5%BB%BA%E5%AE%B9%E5%99%A8.png">



#### 运行容器

```shell
# -i --interactive 交互模式  / -d 后台运行  不能共存
# -t --tty 分配伪终端
# --name 容器命名
docker run -i -t <image>:<tag> /bin/bash
# eg
docker run -i -t --name=mycontainer debian:jessie /bin/bash
```

<img src="https://rex-wangyy.github.io/Imgs/docker/运行容器.png">



#### 查看容器

```shell
# 查看容器(正在运行) -a 所有
# -l --latest 最后创建的容器 
# -n <num> --last <num> 最近创建的num个容器
# -f --filter "key=value" 过滤
docker ps -a
# eg
docker ps -a -f "name=mycontainer"
```

<img src="https://rex-wangyy.github.io/Imgs/docker/查看容器.png">



#### 启动/停止容器

```shell
# 启动容器 可传入多个容器名
docker start <container1> <container2>
# eg
docker start mycontainer

# 停止容器 -t --time 停止时间 默认10s
docker stop <container1> <container2>
# eg
docker stop mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/启动或停止容器.png">



#### 暂停/恢复容器

```shell
# 暂停容器 不会关闭进程
docker pause <container1> <container2>
# eg
docker pause mycontainer

# 恢复容器
docker pause <container1> <container2>
# eg
docker unpause mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/暂停或恢复容器.png">



#### 重启容器

```shell
# command
docker restart <container1> <container2>
# eg
docker restart mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/重启容器.png">



#### 删除容器

```shell
# command
docker rm <container1> <container2>
# eg
docker rm mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/删除容器.png">



#### 查看进程信息

```shell
# 查看进程信息 支持ps的参数
docker top <container>
# eg
docker top mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/查看容器进程信息.png">



#### 查看容器详细信息

```shell
# command
docker inspect <container>
docker inspect -f {{.<key1>.<key2>}} <container>
# eg
docker inspect mycontainer
docker inspect -f {{.State.Running}} mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/查看容器详细信息.png">



#### 查看log

```shell
# -f --follow 持续输出
docker logs <container>
# eg
# 构建一个持续输出的容器
docker run -d --name logs_demo debian:jessie /bin/bash -c 'for((i=0;1;i++));do echo "time $i";sleep 1;done;'
# -f --follow 持续输出
docker logs logs_demo
docker logs -f --tail 5 logs_demo
# --tail 最后几行输出
docker logs --tail 5 logs_demo
# --since 指定时间戳之后的内容
docker logs --since 2021-04-10T21:44:55.123456789Z logs_demo
```

<img src="https://rex-wangyy.github.io/Imgs/docker/容器查看log.png">



#### 连接容器

```shell
# command
docker attach <container>
# eg
docker attach mycontainer
```

<img src="https://rex-wangyy.github.io/Imgs/docker/连接容器.png">



#### 在容器中执行命令

```shell
# 在容器中执行命令
docker exec <container> <command>
# 进入交互模式
docker exec -i-t <container> /bin/bash
# eg
docker exec mycontainer tail /var/log/nginx/access.log
docker exec -i-t mycontainer /bin/bash
```

<img src="https://rex-wangyy.github.io/Imgs/docker/容器中执行命令.png">



#### 提交容器修改

```shell
# command
docker commit -m"<message describe commit>" <container> <namespace>/<image>:<tag>
# eg
docker commit -m"msg" mycontainer wyy/mycontainer:latest
```

<img src="https://rex-wangyy.github.io/Imgs/docker/容器提交.png">



#### 容器导出/导入

```shell
# 容器导出
docker export -o <tar file> <container>
# eg
docker export -o mycontainer.tar mycontainer

# 导入容器（导入后会被持久化伪镜像，不在容器列表里）
docker import <tar file>
# eg
docker import mycontainer.tar
```

<img src="https://rex-wangyy.github.io/Imgs/docker/容器导入导出.png">

