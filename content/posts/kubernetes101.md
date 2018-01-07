---
title: "Kubernetes101"
date: 2018-01-07T23:31:08+08:00
---

这是一篇 kubernetes（简称 k8s，将中间的 8 个字母简写成“8”） 的基础教程，会使用实际例子来讲解 k8s 中的基础概念。

在这个教程中，你将会学习到以下几点：

* 搭建 k8s 集群
* 部署一个容器化的应用到集群中
* 通过 Service 暴露服务
* 通过 Replica 来伸缩服务实例
* 通过 Rolling Update 更新服务
* Deployment, Pod, Node, Service 等相关的概念

## 安装 cli

[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 是 k8s 的命令行工具，可以方便地管理集群

## 安装集群

有两种方式可以安装 k8s 集群

### 云上安装 k8s

如果你有多台云服务器，使用 [rancher](http://rancher.com/) 来搭建 k8s 集群是一个方便快捷的选择。

一般来说，国外的解决方案在中国很可能出现水土不服的情况，需要额外参考这篇文章——[原生加速中国区Kubernetes安装](https://www.cnrancher.com/kubernetes-installation/)

额外，还需要注意以下几点:

* 云服务器的名字不能过长，超过 63 个字符之后，会导致该节点无法注册成功。
* 需要使用和 rancher 兼容的 docker 版本，参考这个[列表](http://rancher.com/docs/rancher/v1.6/en/hosts/#supported-docker-versions)

搭建好集群之后，点击 UI 界面上的 **kubernetes > cli > generate config**，按照说明配置 kubectl ，即可将 kubectl 连接上 rancher 搭建的 k8s 集群

### 本机安装 k8s

本机使用 [minikube](https://github.com/kubernetes/minikube) 来安装 k8s 集群

同样由于水土不服, 请参考这篇文章——[Minikube - Kubernetes本地实验环境](https://yq.aliyun.com/articles/221687)

安装好之后，

```shell
$ kubectl version
```

可以看到客户端和服务器的版本:

```shell
Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.0", GitCommit:"925c127ec6b946659ad0fd596fa959be43f0cc05", GitTreeState:"clean", BuildDate:"2017-12-15T21:07:38Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"windows/amd64"}
Server Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.0", GitCommit:"0b9efaeb34a2fc51ff8e4d34ad9bc6375459c4a4", GitTreeState:"clean", BuildDate:"2017-12-27T05:32:18Z", GoVersion:"go1.9.1", Compiler:"gc", Platform:"linux/amd64"}
```

这说明 k8s 集群已经成功启动，并且 kubectl 已经连接至 k8s 集群

##  Deployment

![](images/20180106/module_02_first_app.png)

集群成功启动之后，为了让容器化(containerized)的应用（比如，一个打好 docker 镜像的 node.js web server）运行在 k8s 中，我们需要创建一个 Deployment。这个 Deployment 会告诉 k8s 如何去创建和更新应用的实例，k8s 会选择节点(Node)并在之上启动应用，同时还有持续监控我们应用的健康状况，一旦应用挂掉了，k8s 会自动重建这个应用的实例。这个**自我修复机制(Self-healing Mechanism)**用来应对节点的失效以及更新维护。

以一个简单的 express hello world 应用为例

```js
// index.js: 
const app = require('express')()

app.get('/', (req, res) => {
    res.send('hello world');
});

app.listen(3000, () => {
    console.log('Server listening at port 3000');
});
```
上面的代码启动了一个 express 服务器，监听 3000 端口。

``` dockerfile
## Dockerfile:
FROM node
RUN mkdir /usr/app/
WORKDIR /usr/app/
COPY package.json package.json
COPY package-lock.json package-lock.json
RUN npm install
COPY index.js index.js
CMD ["node", "index.js"]
EXPOSE 3000
```

上面的 Dockerfile 声明在 node 这个基础镜像上构建，启动命令是 **node index.js**，声明对外暴露 3000 端口(这里的 EXPOSE 只是声明，不一定要和应用实际的监听的端口一样。比如一个应用可以同时监听 3000、4000、5000 端口, 虽然它只声明暴露 3000 端口，但其实 4000，5000 端口仍然是对外暴露的😂)

构建 docker 镜像，并 push 到 docker hub 中（需要登陆 docker id）
```shell
docker build -t ccccly/express-app:v1 .
docker push ccccly/express-app:v1
```

静静等待 push 好之后，我们就可以在 k8s 中通过这个镜像，创建一个 Deployment
``` shell
$ kubectl run express-app --image=docker.io/ccccly/express-app:v1
```
这条命令会做以下三件事：

* 寻找合适的节点来运行 express-app
* 拉取镜像，让它在节点上跑起来
* 配置监控 express-app，确保服务可用

创建好之后
```shell
## 获取 deployment 列表
$ kubectl get deployment
```
发现我们已经创建好了一个 Deployment 
```
NAME          DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
express-app   1         1         1            1           1h
```

## Pod 和 Node

![](images/20180106/module_03_pods.svg)

一个 Deployment 会创建 Pod 来启动应用。在 k8s 中，Pod 是一组（一个或多个）容器以及这些容器公用的存储、网络等资源。它是 k8s 的最小单元。
一个 Pod 中可以有多个容器，这些容器共用一个 IP 和端口，一起被调度，在相同的 Node 上的同一个 Context 下运行。

![](images/20180106/module_03_nodes.svg)

Pod 永远在 Node 上运行。在 k8s 中，Node 是一个工作机器(Worker)，它可以是虚拟机也可以是物理机。一个 Node 上可以运行多个 Pod，k8s 里的 Master 节点自动分配 Pod 到 Node 上运行

继续刚才的 express-app 里例子
```shell
## 获取 pod 列表
$ kubectl get pod
```
可以看到 express-app 的 Pod 正在运行
```
NAME                           READY     STATUS    RESTARTS   AGE
express-app-6b588847fc-dthlr   1/1       Running   0          1d
```
看一下这个 Pod 中的详细信息
```
$ kubectl describe pod express-app-6b588847fc-dthlr
```
查看这个 Pod 的日志
```
$ kubectl logs express-app-6b588847fc-dthlr
```
在 Pod 中执行 bash 
```shell
$ kubectl exec -it express-app-6b588847fc-dthlr bash
### 在容器中 curl 一下，可以看到「hello world」的返回
curl localhost:3000
### 退出
exit
```

## Service

![](images/20180106/module_04_services.svg)

Pod 有自己的生命周期，当一个 Node 死亡之后，Node 上的 Pod 也就消失了。为了容灾和负载均衡，通常会在 k8s 中运行多个应用的实例，也就有了多个 Pod。
每一个 Pod 都有自己的唯一的 IP 地址，一个 Pod 死亡重建之后就有了一个新的 IP 地址，所以 Pod 的 IP 地址是不确定的。然而服务的消费者只会使用固定的 IP 地址，所以需要有一种机制，来掩盖掉这些底层 IP 地址的变化，消除消费者和 Pod 的耦合。

Service 就是这样一种抽象机制，它定义了一组 Pod 和相应的访问机制。Pod 的 IP 不对集群外暴露，Service 则可以对外暴露。Service 内置 LoadBalancer 来将请求转发到不同的 Pod 上。

Service 一共有四种暴露机制：

* ClusterIP（默认）：通过集群的内部 IP （例如，10.0.0.151）暴露 Service，只能被集群内部的消费者访问
* NodePort：使用 [NAT](https://en.wikipedia.org/wiki/Network_address_translation) 机制在 Node 上开启相同的 Port 来暴露服务，可以用 **NodeIP:NodePort** （例如，http://192.168.99.100:31530 ）来访问
* LoadBalancer：使用云服务厂商提供的外部负载均衡器来暴露服务，会是一个固定的外部 IP
* ExternalName：通过 CNAME 来暴露服务

继续刚才的 express-app 的例子

使用 NodePort 来暴露 express-app，Service 的端口是 80，目标端口是应用对外暴露的端口 3000
```shell
$ kubectl expose deployment express-app --type NodePort --port 80 --target-port 3000
$ kubectl get service
NAME          TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
express-app   NodePort    10.0.0.151   <none>        80:31530/TCP   19m
kubernetes    ClusterIP   10.0.0.1     <none>        443/TCP        1d
```
这时候可以使用 **NodeIP:NodePort** 来从外界访问这个服务
```shell
$ curl http://192.168.99.100:31530
```

## Scaling

为了保持 [SLA](https://en.wikipedia.org/wiki/Service-level_agreement)（比如其中的一种 SLA，服务访问性可达到 99.999%，内业经常说你的服务能达到多个 9，哈哈），通常会部署多份相同的服务，运行在多个 Pod 上。在 k8s 中，通过修改 Deployment 的 Replica（副本） 数量来达到 scaling 的目的。

将 express-app 从 1 个 Replica, Scale 到 4 个 Replica
![](images/20180106/module_05_scaling1.svg)
```shell
$ kubectl scale deployment express-app --replicas 4
```

查看 Scale 后的 Deployment
![](images/20180106/module_05_scaling2.svg)
```
$ kubectl get deployment
NAME          DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
express-app   4         4         4            4           1d
```

## Rolling Update

消费者希望服务不间断，开发者需要频繁地更新应用。在 k8s 中，使用 Rolling Update 来更新应用，同时确保服务的连续可用性。

在更新应用时，Rolling Update 先创建好一个更新之后的 Pod，再替换掉老的 Pod，以此来满足服务的连续性。

Rolling Update 根据 max-unavailable（一次更新中，最多有几个 Pod 不可用） 和 max-surge （一次更新中，最多可以多创建几个应用）两个参数来执行更新策略。

继续 express-app 的例子。

修改 index.js，使它返回「hello world v2」

```shell
### 重新 build 镜像
$ docker build -t ccccly/express-app:v2 .
$ docker push ccccly/express-app:v2
```

执行 Rolling Update
```shell
$ kubectl set image deployment express-app express-app:v1=docker.io/ccccly/express-app:v2
NAME                           READY     STATUS              RESTARTS   AGE
express-app-7b747f7cd9-2xtrm   1/1       Terminating         0          6m
express-app-7b747f7cd9-4scjw   1/1       Terminating         0          6m
express-app-7b747f7cd9-kwsqx   1/1       Running             0          6m
express-app-7b747f7cd9-rk7hm   1/1       Terminating         0          6m
express-app-7d5b96f8b-cvx85    1/1       Running             0          7s
express-app-7d5b96f8b-gpqsw    1/1       Running             0          8s
express-app-7d5b96f8b-h4mxt    0/1       ContainerCreating   0          0s
express-app-7d5b96f8b-wp4db    0/1       ContainerCreating   0          1s
```
发现这次更新时，Pod 有三种状态，Running, ContainerCreating 和 Terminating

Update 完之后，测试一下
```shell 
$ curl http://192.168.99.100:31530
hello world v2
```
发现应用已经更新😘

## 结语

至此，这篇教程已经结束，大家欢快地去玩耍一下 k8s 吧😄 如果在搭建和使用集群时，遇到任何困难，欢迎 ping 我。





