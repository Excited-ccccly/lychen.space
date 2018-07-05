---
title: "Kubernetes Ingress"
date: 2018-03-27T15:27:27+08:00
authors: ["ccccly"]
categories: ["DevOps"]
tags: ["Kubernetes", "Ingress", "nginx", "Loadbalancer"]
---

[kubernetes101](https://studymakesmehappy.club/posts/kubernetes101/) 介绍了 Kubernetes 的基本用法，通过 NodePort 的形式对外暴露 Service 来提供服务。

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

但每次使用 IP+Port 访问服务的方式很不方便。在单机上，可以使用 nginx 的反向代理，实现通过域名访问服务。

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

启用 nginx 后，就可以通过访问 http://api.domain.com 来访问这台机子上监听 8080 端口的服务。

在 Kubernetes 集群中，Ingress 可以说是对 nginx 的抽象（接口），[ingress-nginx](https://github.com/kubernetes/ingress-nginx) 是 Ingress 的一种实现。

在这篇博客中，假设你学习过 [kubernetes101](https://studymakesmehappy.club/posts/kubernetes101/)，你将会学习到以下几点：

* Ingress 介绍
* Ingress 的基础配置
* Ingress 的 TLS 配置
* Ingress 的 basic auth 配置
* Ingress 的局限

## Ingress 介绍

在 Kubernetes 中，通过 Ingress 访问应用的流程大致上是这样子的

![求推荐好用的画图工具，Visio 好难用...](/images/kubernetes/ingress/ingress.png)

除了 Ingress 暴露在公网中，其它所有的服务都是内网 IP，只允许内网访问。

* External LoadBalancer 是云服务厂商提供的外部负载均衡器，比如 Azure LoadBalancer, AWS ELB 等。
* Ingress Controller 根据定义的 Ingress 规则，来转发不同的请求到不同的 Service 上，就像 nginx 的反向代理一样。
* Default Backend 是默认的后端，但有一个未知的域名(ingress 中没有定义)的请求过来，将会返回 default backend 提供的内容。

一个简单的 Ingress 规则长这样子

```YAML
apiVersion: v1
items:
- apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: kube-system.ingress
    namespace: kube-system
  spec:
    rules:
    - host: website1.domain.com
      http:
        paths:
        - backend:
            serviceName: website1
            servicePort: 80
          path: /
kind: List
```

此项规则将 website1.domain.com 的请求全部转发到 website1 服务对应的 Pod 中。

## Ingress 的基础配置

接下来动手配置一个 Kubernetes 集群的 Ingress。

> 由于以下的步骤要求部署的 Kubernetes 支持云厂商提供的外部 LoadBalancer，类似于 minikube 之类的环境是不支持外部 LoadBalancer 的，请知晓。

为了方便展示，以下所有使用到的配置文件都使用网络地址，不显示实际的内容，可以单独下载文件查看，也可以前往 [ingress 配置文件夹](https://github.com/Excited-ccccly/studymakesmehappy.club/tree/master/static/files/kubernetes/ingress) 查看所有文件

* 为了方便管理，我们为 Ingress 单独创建一个 namespace

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/01-namespace.yaml
```

* 创建默认的 http 后端，Ingress 接收到一个 404 的地址，交给这个后端来处理

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/02-default-backend.yaml
```

* 创建 ingress nginx 所需的配置文件。默认的配置限制 body size 为 1m，这里稍微调大一些，方便文件上传

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/03-configmap.yaml
```

* 创建 ingress nginx 服务

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/04-ingress-nginx.yaml
```

* 通过 LoadBalancer 的方式对外暴露 ingress nginx 服务

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/05-service.yaml
```

  获取 ingress nginx 服务的外部 LoadBalancer 的地址

```shell
  kubectl get svc --namespace ingress-nginx
```

* 将域名解析到外部的 LoadBalancer

    *.domain.com -> IngressNginxExternalLoadBalancerIP

* 创建一条 Ingress 规则(暂时不带 TLS 和 Basic Auth)，将 **k8s-dashboard.domain.com** 转发至 kubernetes-dashboard 服务对应的 Pod 中。

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/06-k8s-dashboard-ingress-without-neither-tls-or-basic-auth.yaml
```

ingress-controller 会监听集群中所有 Ingress 规则的变动，保持更新。等待大约 10s，就可以尝试访问 **k8s-dashboard.domain.com** 了，看看是否是熟悉的 kubernetes dashboard.

## Ingress 的 TLS 配置

由于 HTTP 很不安全，容易遭受中间人攻击，现在的网站流行 HTTPS 认证，在此为 **k8s-dashboard.domain.com** 加上 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security)，点亮 HTTPS 的绿灯。

HTTPS 证书需要购买，也可以使用 Let's Encrypt 提供的免费 HTTPS 证书。[kube-lego](https://github.com/jetstack/kube-lego) 可以简化从 Let's Encrypt 获取证书的流程。但由于 Let's Encrypt 每90天需要更新一次证书，还可能会出现因为获取证书错误次数过多，获取不到证书，从而只能干等的状况，所以生产环境务必使用购买的 HTTPS 证书。

购买的 HTTPS 证书一般会有两个文件，domain.key 和 domain.pem

* 创建 TLS secret

```shell
kubectl create secret tls domain-tls --cert=path/to/domain.pem --key=path/to/domain.key
```

* 创建好 domain-tls 的 secret 后，更新 Ingress 规则，加上 TLS 配置

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/06-k8s-dashboard-ingress-with-tls.yaml
```

HTTPS 生效大概需要1分钟，之后访问 **k8s-dashboard.domain.com**，绿色的 HTTPS 认证就出现了。

## Ingress 的 basic auth 配置

加入 TLS 加密之后，保证了客户端和服务器之间的数据传输是受保护的，但是现在任何一个人都可以通过 **k8s-dashboard.domain.com** 访问并控制 kubernetes 集群，这是不可接受的，需要加上权限验证。

简单起见，以 basic-auth 为例

* 创建 basic auth 文件，这里创建了一个名为 auth，用户名是 foo，密码是输入的密码的 basic auth 文件。Windows 下没有 htpasswd，可以使用 Windows Subsystem for Linux 来生成，详情请参考 [Set Up Development Environment on Windows](https://studymakesmehappy.club/posts/set-up-development-environment-on-windows/)

```shell
$ htpasswd -c auth foo
New password: <bar>
New password:
Re-type new password:
Adding password for user foo
```

* 创建 basic auth 的 secret

```shell
kubectl create secret generic basic-auth --from-file=auth
```

* 更新 Ingress，在 annotation 中加入 basic auth 的配置

```shell
kubectl apply -f  https://raw.githubusercontent.com/Excited-ccccly/studymakesmehappy.club/master/static/files/kubernetes/ingress/06-k8s-dashboard-ingress-with-tls-and-basic-auth.yaml
```

也是大概需要等1分钟，之后访问 k8s-dashboard.ruffcorp.com 都需要输入密码。

## Ingress 的局限

通常来说，nginx 是第7层(Application Layer In [OSI network model](https://en.wikipedia.org/wiki/OSI_model))的负载均衡器，虽说 nginx 有 TCP 插件，可以转发第4层（Transport Layer Layer）的 TCP 连接，但 Ingress-nginx 对 [TCP 请求的转发规则的支持](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/exposing-tcp-udp-services.md) 可能不能满足我们的需求。

假设我们有三个环境——Production, Staging 和 Test，三个环境对应3个命名空间(namespace)，三个环境下都有一个 tcp 服务，在443端口提供服务。它们域名分别为:

* tcp.domain.com
* staging-tcp.domain.com
* test-tcp.domain.com

由于 Ingress-nginx 只能根据端口来转发 tcp 请求，而不是 **域名:端口** 的形式

```shell
443: production/tcp-service
```

但是这三个环境下的 tcp 服务的端口都是一样的，就会出现这种情况

```shell
443: production/tcp-service
443: staging/tcp-service
443: test/tcp-service
```

显然 Ingress nginx 无法识别这样的规则。

假如 tcp 的规则可以这样设置：

```shell
"tcp.domain.com:443": production/tcp-service
"staging-tcp.domain.com:443": staging/tcp-service
"test-tcp.domain.com:443": test/tcp-service
```

那么 nginx 就能满足我们3个环境的需求了。但这是无法实现的，在 TCP 层没有 host 域名信息，所以这个设想是无法做到的。

针对这3个环境下的 tcp 服务，类似于 ingress nginx 的方式，可以单独通过 LoadBalancer 类型的 Service 对外暴露，获取到外部 IP 之后，再手动设置 DNS 解析。

```shell
kubectl --namespace=production expose deploy tcp-service --type=LoadBalancer --port=80 --target-port=80 
```

获取 tcp-service 服务的外部 LoadBalancer 的地址

```shell
kubectl get svc --namespace ingress-nginx
```

将域名解析到外部的 LoadBalancer

tcp.domain.com -> TcpServiceExternalLoadBalancerIP


有问题欢迎 ping😁