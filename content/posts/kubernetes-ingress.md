---
title: "Kubernetes Ingress"
date: 2018-03-27T15:27:27+08:00
draft: true
---

[kubernetes101](./kubernetes101.md) 介绍了 Kubernetes 的基本用法，通过 NodePort 的形式对外暴露 Service 来提供服务。

```shell
NAME          TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
express-app   NodePort    10.0.0.151   <none>        80:31530/TCP   19m
kubernetes    ClusterIP   10.0.0.1     <none>        443/TCP        1d
```
这时候可以使用 **NodeIP:NodePort** 来从外界访问这个服务，其中 192.168.99.100 是集群中任意一个 Node 节点的 IP
```shell
$ curl http://192.168.99.100:31530
hello world
```
但每次使用 IP+Port 访问服务的方式很不方便。在单机上，可以使用 Nginx 的反向代理，实现通过域名访问服务。

nginx.conf
```shell
http {
  upstream my-api {
    server 127.0.0.1:8080;
  }

  server {
    listen 80;
    server_name api.domain.com;
    location / {
      proxy_pass http://my-api;
    }
  }
}
```
启用 Nginx 后，就可以通过访问 http://api.domain.com 来访问这台机子上监听 8080 端口的服务。

在 Kubernetes 集群中，Ingress 就是对 Nginx 的抽象（接口），[ingress-nginx](https://github.com/kubernetes/ingress-nginx) 是 Ingress 的一种实现。

在这篇博客中，假设你学习过[kubernetes101](./kubernetes101.md)，你将会学习到以下几点：

* Ingress 的基础配置
* Ingress 的 TLS 配置
* Ingress 的 basic auth 配置
* Ingress 的局限

## Ingress 的基础配置

为了方便管理，我们为 Ingress 单独创建一个 namespace
```shell
$ kubectl create namespace ingress-nginx
```

