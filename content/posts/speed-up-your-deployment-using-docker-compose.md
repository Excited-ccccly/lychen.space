---
title: "Speed Up Your Deployment Using Docker Compose"
date: 2018-02-12T17:36:42+08:00
draft: true
---

Docker 的出现，优秀的隔离性让我们可以将任何应用部署到任何服务器上面，不再需要关注服务器的环境配置，简化了单个应用的部署流程。但更多的情况下，应用往往会依赖于其他服务，比如前端网页依赖于后端服务，后端服务依赖于数据库。我们可以依次部署每个服务，但每次重复的手工操作让人厌烦。我们需要一个工具，只要描述好整套系统之间的服务依赖关系，将这个描述文件交给这个工具，一条命令便能启动整个系统。这种类型的工具有很多，这篇博客里聊的是**Docker Compose**。

## Overview

Docker Compose 用于定义和运行多容器系统，它使用 YAML 文件来描述服务之间的依赖关系，一条简单的*docker compose up*命令便能启动你的整套系统。
Compose 多用于单主机部署，比如在本地电脑上搭建你的开发环境，CI／CD 服务器上搭建集成测试环境等简单场景。当然 Docker 官方说 Compose 也可以用于生产环境，只要你用 Swarm 就好了。但目前的情况是，Kubernetes 大行其道，Swarm 日渐式微，就连 Docker 官方也都迫不得已拥抱了 Kubernetes。现在生产环境多用 Kubernetes 来部署管理多容器应用，如果你对 Kubernetes 感兴趣，可以参考我之前的入门文章[Kubernetes101](http://studymakesmehappy.club/posts/kubernetes101/)。

我个人不是任何技术的信徒，每种技术都有它存在的道理。Docker Compose 简单方便，不需要花费精力部署调试 Kubernetes，适用于开发、测试以及个人的小应用场景；Kubernetes 成熟、自由、社区资源丰富，适用生产环境。建议大家可以亲自动手实践一下这两项技术，说实话，也花不了一晚上的时间。亲身体验之后，才能给技术恰如其分地归类，知道它们适用的场景，需要的时候顺手就用好了。

## Setup

## Compose Up

## Environment Variable Injection

## Volume Mount

## Service Dependency

## Conclusion
