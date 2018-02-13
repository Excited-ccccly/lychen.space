---
title: "Speed Up Your Deployment Using Docker Compose"
date: 2018-02-14T01:05:40+08:00
categories: ["DevOps"]
tags: ["Docker", "Compose", "Deployment"]
---

Docker 的出现，优秀的隔离性让我们可以将任何应用部署到任何服务器上面，不再需要关注服务器的环境配置，简化了单个应用的部署流程。但在更多的情况下，应用往往会依赖于其他服务，比如前端网页依赖于后端服务，后端服务依赖于数据库。我们可以依次部署每个服务，但每次重复的手工操作让人厌烦。我们需要一个工具，只要描述好整个应用之间的服务依赖关系，将这个描述文件交给这个工具，一条命令便能启动整个系统。这种类型的工具有很多，这篇博客里聊的是**Docker Compose**。

本文假设你有基本的 Docker 使用基础，你将会学习到以下几点：

* Docker Compose 是什么？它适用于哪些场景？
* 安装和使用 Docker Compose
* 注入环境变量来配置你的程序
* 挂载磁盘来持久化你的容器数据

## Overview

Docker Compose 用于定义和运行多容器应用，它使用 YAML 文件来描述服务之间的依赖关系，一条简单的**docker compose up**命令便能启动你的整个应用。
Compose 多用于单主机部署，比如在本地电脑上搭建你的开发环境，CI／CD 服务器上搭建集成测试环境等简单场景。当然 Docker 官方说 Compose 也可以用于生产环境，只要你用 Swarm 就好了。但目前的情况是，Kubernetes 大行其道，Swarm 日渐式微，就连 Docker 官方也都迫不得已拥抱了 Kubernetes。现在生产环境多用 Kubernetes 来部署管理多容器应用，如果你对 Kubernetes 感兴趣，可以参考我之前的入门文章[Kubernetes101](http://studymakesmehappy.club/posts/kubernetes101/)。

我个人不是任何技术的信徒，每种技术都有它存在的道理。Docker Compose 简单方便，不需要花费精力部署调试 Kubernetes，适用于开发、测试以及个人的小应用场景；Kubernetes 成熟、自由、社区资源丰富，适用生产环境。建议大家可以亲自动手实践一下这两项技术，说实话，也花不了一晚上的时间。亲身体验之后，才能给技术恰如其分地归类，知道它们适用的场景，需要的时候顺手就用好了。

## Setup
Windows 和 Mac 下的 Docker 客户端自带 Docker Compose， Ubuntu 下直接**apt install docker-compose**即可

详情参考[Install Docker Compose](https://docs.docker.com/compose/install/) 
## Compose Up
 以一个简单的 express app 为例

```yaml
## docker-compose.yaml
version: "2"
services:
  mysql:
    image: "mysql"
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "123"
  app:
    image: "ccccly/express-app:docker-compose"
    ports:
      - "3000:3000"
    environment:
      MYSQL_HOST: "mysql"
      MYSQL_USER: "root"
      MYSQL_PASSWORD: "123"
```

可以看出，app 使用的 Docker 镜像是 ccccly/express-app:docker-compose，在 3000 端口上对外提供服务，app 依赖于一个 mysql 服务，mysql 的用户名是 root，密码是 123。被依赖的 mysql 服务使用的镜像是 mysql，在 3306 端口上对外提供服务。

这时候，使用
```shell
## 默认使用当前目录下面的 docker-compose.yaml 文件，如果你的 compose 文件不是这样子命名的，你需要额外加上 compose 的文件名
$ docker-compose up
```

就能启动这个由 express-app 和 mysql 组成的应用了。

app 的代码是这样子的:

```js
app.get('/', (req, res) => {
  pool.getConnection((err, connection) => {
    if (err) return;
    connection.query('SELECT 1 + 1 AS solution', (err, results, fields) => {
      connection.release();
      if (err) {
        console.log(err);
      }
      const solution = results[0].solution;
      res.json({ solution });
    });
  });
});

app.listen(3000);
```
有 '/' 的请求来时，执行 'SELECT 1 + 1 AS solution' 的 sql 语句，返回给客户端

```shell
$ curl localhost:3000
{"solution": 2}
```

## Environment Variable Injection
在配置 mysql 的连接时，由于本机开发环境和线上环境是不一样的，如果 mysql 连接直接写死在代码中，那么每次部署，我们都需要修改代码来连接相应的数据库，这是很不干净的做法。为了确保本机和线上的代码是一致的，我们需要使用环境变量(Environment Variable)将配置 mysql 连接信息。

环境变量其实就是程序运行的环境中一些参数的值，比如**NUMBER_OF_PROCESSORS=8**这个环境变量就告诉操作系统运行中的所有软件，“这台电脑的处理器有 8 个，请你们按需使用”；**PATH=C:\Users\GeekC\go\bin;**告诉操作系统说这个文件夹下面有一些可执行文件，其它地方找不到的话，你可以来这边找找(go)。很多小伙伴喜欢使用 nvm 来管理自己的 node 版本，其实就是通过修改环境变量或者软链接的方式来更换可执行文件(node)的寻找地址。

回到正题，关于在代码中通过环境变量注入 mysql 的连接信息。我们首先在代码中这样配置 mysql 连接:
```js
const pool = mysql.createPool({
  host: process.env.MYSQL_HOST || 'localhost',
  user: process.env.MYSQL_USER || 'root',
  password: process.env.MYSQL_PASSWORD || '123'
});
```
意思就是，如果环境变量 MYSQL_HOST 有值，那么就 host 就使用这个值，否则使用 localhost。MYSQL_USER 和 MYSQL_PASSWORD 同理。

在本机开发的时候，由于本机没有配置环境变量，则默认使用 localhost。

在线上部署的时候，通过在 Compose 文件定义环境变量:
```yaml
environment:
  MYSQL_HOST: "mysql"
  MYSQL_USER: "root"
  MYSQL_PASSWORD: "123"
```
这时候，host 就变成了 "mysql"，它是一个服务名，对应 Compose 中的 mysql 服务。在 Compose 文件中，服务与服务之间的连接使用服务名来寻址的方式。docker-compose 在启动的时候，会自动把服务名和服务对应的 IP 地址写入到每个容器里的 HOST(负责 DNS 解析) 文件中。

通过注入环境变量，确保了代码和环境配置的松藕合，更利于自动化部署。
## Volume Mount
一旦容器被删除，容器里数据也就丢失了。显然，我们不希望看到 mysql 中存储的数据就这么随容器的消亡而消失，我们需要持久化 mysql 中存储的数据。在 Docker 中，通过使用挂载磁盘的方式，将容器中需要存储的数据保存在挂载的磁盘中。

只需要在 mysql 中加一条配置，就可以将 mysql 中的数据存储到主机的 ./data 目录中
```yaml
services:
  mysql:
    volumes:
      - ./data:/var/lib/mysql
```
另一方面，容器也可以通过挂载磁盘来使用外部的配置文件，比如 nginx
```yaml
services:
  nginx:
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
```
最后面的 :ro 的意思是 read only，因为配置文件不允许在程序运行的时候被改动

## Conclusion

在单主机上部署多容器应用时，Docker Compose 是个不错的选择。它胜在简单方便，在没有也不需要 Kubernetes 的时候，可以尝试一下 Docker Compose，可以显著提升我们产品的交付部署效率。

最后，提供一份前两天写的一个 Compose 文件，供大家参考。
```yaml
version: '2'                                    
services:                                     
  rabbitmq: // mqtt broker                          
    image: "cyrilix/rabbitmq-mqtt" // Docker 镜像     
    container_name: "rabbitmq" // 启动后的容器的名字
    restart: "always"  // 失败后自动重启                   
    hostname: "rabbitmq-hostname" // rabbitmq 需要知道主机的名字                        
    ports:                                      
      - "1883:1883" // 声明开放的端口                             
    environment:                                
      RABBITMQ_DEFAULT_USER: "rabbitmq-user" // 用户名         
      RABBITMQ_DEFAULT_PASS: "rabbitmq-password" // 密码   
  influxdb:                                     
    image: "influxdb" // 时序数据库                       
    container_name: "influxdb"                  
    restart: "always"                           
    ports:                                      
      - "8086:8086"                             
    volumes:                                    
      - ~/docker-data/influxdb:/var/lib/influxdb // 时序数据库的数据持久化在主机的 ~/docker-data/influxdb 目录
    environment:                                
      INFLUXDB_DB: "influxdb-database"                   
      INFLUXDB_HTTP_AUTH_ENABLED: "true"        
      INFLUXDB_ADMIN_USER: "influx-admin-user"            
      INFLUXDB_ADMIN_PASSWORD: "influxdb-admin-password"   
      INFLUXDB_USER: "influxdb-user" // 数据库访问的用户名
      INFLUXDB_USER_PASSWORD: "influxdb-user-password" // 数据库访问的密码     
  forwarder:                                    
    image: "mqtt-influxdb-forward" // 自己写的消息转发服务，将 mqtt 消息经过处理之后，转发到 influxdb
    container_name: "mqtt-influxdb-forward"     
    restart: "always"                           
    depends_on: // 声明该服务依赖  rabbitmq 和 influxdb 服务                                 
      - rabbitmq                                
      - influxdb                                
    environment:                                
      MQTT_ENDPOINT: "mqtt://rabbitmq"          
      MQTT_USERNAME: "rabbitmq-user"                 
      MQTT_PASSWORD: "rabbitmq-password"              
      MQTT_TOPIC: "test/topic"                    
      INFLUX_HOST: "influxdb"                   
      INFLUX_DATABASE: "influxdb-database"               
      INFLUX_USERNAME: "influxdb-user"               
      INFLUX_PASSWORD: "influxdb-user-password"            
      INFLUX_WRITE_THRESHOLD: 10                
  grafana: // 数据可视化框架，从 influxdb 取数据，使用图表的方式展示出来
    image: "grafana/grafana"                    
    container_name: "grafana"                   
    restart: "always"                           
    ports:                                      
      - "3000:3000"                             
    environment:                                
      GF_SECURITY_ADMIN_USER: "grafana-user"        
      GF_SECURITY_ADMIN_PASSWORD: "grafana-passowrd"
``` 
