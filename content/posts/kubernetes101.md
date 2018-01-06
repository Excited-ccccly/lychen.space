---
title: "Kubernetes101"
date: 2018-01-06T14:55:38+08:00
draft: true
---

这是一篇 kubernetes（简称 k8s，将中间的 8 个字母简写成“8”） 的基础教程，会使用实际例子来讲解 k8s 中的基础概念。

在这个教程中，你将会学习到以下几点：

* 搭建 k8s 集群
* 使用 kubectl 管理集群
* 部署一个容器化的应用到集群中
* 缩放容器的实例数量
* 

## 安装 cli

[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 是 k8s 的命令行工具，可以方便地管理我们的集群

## 安装集群

有两种方式可以安装 k8s 集群

### 云上安装 k8s

如果你有多台云服务器，使用 [rancher](http://rancher.com/) 来搭建 k8s 集群是一个方便快捷的选择。

一般来说，国外的解决方案在中国很可能出现水土不服的情况，需要额外参考这篇文章——[原生加速中国区Kubernetes安装](https://www.cnrancher.com/kubernetes-installation/)

额外，还需要注意以下几点:

* 云服务器的名字不能过长，超过 63 个字符之后，会导致该节点无法注册成功。
* 需要选择和 rancher 兼容的 docker 版本，参考这个[列表](http://rancher.com/docs/rancher/v1.6/en/hosts/#supported-docker-versions)

搭建好集群之后，点击 UI 界面上的 **kubernetes > cli > generate config**，按照说明配置 kubectl ，即可将 kubectl 连接上 rancher 搭建的 k8s 集群

### 本机安装 k8s

本机使用 [minikube](https://github.com/kubernetes/minikube) 来安装 k8s 集群

同样由于水土不服, 请参考这篇文章——[Minikube - Kubernetes本地实验环境](https://yq.aliyun.com/articles/221687)

安装好之后，

```shell
kubectl version
```

可以看到客户端和服务器的版本:

```shell
Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.0", GitCommit:"925c127ec6b946659ad0fd596fa959be43f0cc05", GitTreeState:"clean", BuildDate:"2017-12-15T21:07:38Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"windows/amd64"}
Server Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.0", GitCommit:"0b9efaeb34a2fc51ff8e4d34ad9bc6375459c4a4", GitTreeState:"clean", BuildDate:"2017-12-27T05:32:18Z", GoVersion:"go1.9.1", Compiler:"gc", Platform:"linux/amd64"}
```

这说明 k8s 集群已经成功启动，并且 kubectl 已经连接至 k8s 集群

##  创建一个部署(Deployment)

![](images/20180106/module_02_first_app.png)

集群成功启动之后，为了让我们的容器化(containerized)的应用（比如，一个打好 docker 镜像的 node.js web server）运行在 k8s 中，我们需要创建一个部署。这个部署配置会告诉 k8s 如何去创建和更新应用的实例，k8s 会在选择节点(node)并在之上启动我们的应用，同时还有持续监控我们应用的健康状况，一旦应用挂掉了，k8s 会自动重建这个应用的实例。这个**自我修复机制(Self-healing Mechanism)**用来应对节点的失效和更新维护。



