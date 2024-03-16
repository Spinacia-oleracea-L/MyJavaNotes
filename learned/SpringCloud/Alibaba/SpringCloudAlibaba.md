# Nacos

Nacos名称为：Naming and Configuration Service 的首字母缩写

## 概述：

Nacos是⼀个开源的分布式服务发现和配置管理平台，它可以帮助开发者更好地管理微服务架构中的服 务实例和配置信息。Nacos最初由阿⾥巴巴开发并开源，⽬前已经成为Apache基⾦会的顶级项⽬之⼀。

### Nacos提供了以下核心功能：

* Naming服务发现：Nacos可以实时感知服务实例的上下线变化，⽀持多种服务注册⽅式，包括REST、DNS 和RPC等。
* Config配置管理：Nacos可以集中管理所有微服务的配置信息，⽀持动态配置更新和多种配置格式，如 JSON、XML和YAML等。
* Service
  * 服务健康检查：Nacos可以对服务实例进⾏⼼跳检测和健康状态检查，及时发现异常实例并剔除
  * 服务路由与负载均衡：Nacos可以根据多种策略进⾏服务路由和负载均衡，如轮询、随机和加权等
* 服务健康检查：Nacos可以对服务实例进⾏⼼跳检测和健康状态检查，及时发现异常实例并剔除
* Nacos 2相⽐于之前的版本，主要增加了以下特性：

### Nacos 2相比于之前的版本，主要增加了以下特性：

* 分布式配置集群：⽀持将配置信息分布到多个Nacos节点上进⾏管理，提⾼了配置的可靠性和可用性。
* ⾃动拉取配置：Nacos可以⾃动拉取外部配置中⼼的配置信息，并将其同步到本地，保证配置的⼀ 致性和实时性。
* 数据持久化⽀持：⽀持多种数据存储⽅式，包括MySQL、Oracle和TiDB等，同时⽀持数据备份和恢复。
* 更好的性能和可伸缩性：Nacos 2优化了核⼼组件的性能和可伸缩性，⽀持更⾼的并发量和更⼤规模的集群部署。

### Docker快速部署单机Nacos：

```Shell
docker rm -f nacos
docker run -d --name nacos --restart=always \
-e MODE=standalone \
-e NACOS_AUTH_TOKEN=SecretKey012345678901234567890123456789012345678901234567890123456789 \
-e NACOS_AUTH_IDENTITY_KEY=ikey \
-e NACOS_AUTH_IDENTITY_VALUE=idvalue \
-p 8848:8848 -p 9848:9848 -d \
nacos/nacos-server:v2.3.1-slim
```

[关于Nacos默认token.secret.key及server.identity风险说明及解决方案公告](https://nacos.io/zh-cn/blog/announcement-token-secret-key.html)

## 永久实例和临时实例

Nacos 提供了两种服务类型供⽤户注册实例时选择，分为临时实例和永久实例。

### 临时实例：

PS：TTL（Time To Live）机制，即客户端在⼀定时间没有向注册中心发送心跳。

临时实例只是临时存在于注册中心中，会在服务下线或不可⽤时被注册中心剔除，临时实例会与注册中心保持心跳，注册中心会在⼀段时间没有收到来⾃客户端的心跳后会将实例设置为不健康，然后在⼀段时间后进⾏剔除。

#### 在 Nacos 中，⽤户可以通过两种⽅式进⾏临时实例的注册：

* OpenAPI 的注册⽅式实际是⽤户根据⾃身需求调⽤ Http 接⼝对服 务进⾏注册，然后通过 Http 接⼝发送⼼跳到注册中⼼。(长链接)
* SDK 的注册⽅式实际是通过 RPC 与注册中⼼保持连接（Nacos 2.x 版本中，旧版的还是仍然通过OpenAPI 的⽅式），客户端会定 时的通过 RPC 连接向 Nacos 注册中⼼发送⼼跳，保持连接的存活。(5秒一次心态传输)

都是由注册中心主动剔除该 client 所注册的服务。

### 永久实例：

Nacos 中使⽤ SDK 对于永久实例的注册实际也是使⽤ OpenAPI 的⽅ 式进⾏注册，这样可以保证即使是客户端下线后也不会影响永久实例的健康检查。

对于永久实例的的监看检查，Nacos 采⽤的是注册中⼼探测机制，注 册中⼼会在永久服务初始化时根据客户端选择的协议类型注册探活的定时任务。Nacos 现在内置提供了三种探测的协议，即Http、TCP 以及MySQL 。⼀般⽽⾔ Http 和 TCP 已经可以涵盖绝⼤多数的健康检查场景。

## Nacos集群

在docker中，同一网段下不同的容器，可以通过容器名进行彼此的互联通信
