---
layout: post
title: '初识Kubernetes(二)'
date: 2021-04-14
author: Rex
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Kubernetes
---

## 

## Kubernetes 安装



### MAC

```shell
# 下载kubectl组件
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/darwin/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
# 查看kubectl版本
kubectl version
# 下载virtualbox
https://download.virtualbox.org/virtualbox
# 下载minikube
curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.2.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
# 启动minikube
minikube start --vm-driver=virtualbox
# dashboard
minikube dashboard
# URL
http://127.0.0.1:62031/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/#!/overview?namespace=default
```





### Windows 10

```shell
# win10 开启虚拟化
# 在 Windows 上用 Chocolatey (一个包管理器)安装 kubectl  https://chocolatey.org/install
# 打开PowerShell，用管理员身份
# 必须确保Get-ExecutionPolicy不受限制，
Get-ExecutionPolicy
# 返回Restricted
Set-ExecutionPolicy AllSigned
# 安装Chocolatey,执行此命令
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
# 检测是否安装成功
choco -
# 在 Windows 上用 Chocolatey 安装 kubectl
choco install kubernetes-cli
# 检测版本
kubectl version --client
# 切换本地HOME目录
cd C:\Users\user11\ 
# 创建 .kube 目录
mkdir .kube
# 切换到刚刚创建的 .kube 目录
cd .kube
# 配置 kubectl 以使用远程 Kubernetes 集群
New-Item config -type file
# 开启PowerShell（管理员)，执行如下命令，然后重启电脑
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
# 使用 Chocolatey 安装 Minikube
choco install minikube
# 关闭当前 CLI 界面再重新打开,Minikube 应该已经自动添加至 path 中
# 确认 hypervisor 和 Minikube 均已成功安装
```

