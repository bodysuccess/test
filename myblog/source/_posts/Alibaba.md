---
layout: springcloud
title: Alibaba
date: 2021-05-30 18:11:47
tags: springCloud
categories: 框架
---

#### SpringCloud 阿里系列的官网地址

https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html

#### nacos的两大主要功能是服务注册与发现、动态配置服务

#### 服务注册与发现：Spring Cloud Alibaba Nacos Discovery

Nacos提供基于DNS和基于RPC的服务发现，即能被用来支持https/http的服务注册与发现，也支持RPC如dubbo的服务注册与发现。

Nacos是一种去中心化的架构，属于CAP理论里的AP架构，支持最终一致性，在分布式服务发现与注册场景下具有很不错的性能。

|                 | Nacos                          | Eureka      | Consul            | Zookeeper  |
| --------------- | ------------------------------ | ----------- | ----------------- | ---------- |
| 一致性协议      | CP+AP                          | AP          | CP                | CP         |
| 健康检查        | TCP/HTTP/MYSQL/Client          | Beat Client | TCP/HTTP/gRPC/Cmd | Keep Alive |
| 负载均衡策略    | 权重/ metadata/Selector/Ribbon | Ribbon      | Fabio             | —          |
| 雪崩保护        | 有                             | 有          | 无                | 无         |
| 自动注销实例    | 支持                           | 支持        | 不支持            | 支持       |
| 访问协议        | HTTP/DNS                       | HTTP        | HTTP/DNS          | TCP        |
| 监听支持        | 支持                           | 支持        | 支持              | 支持       |
| 多数据中心      | 支持                           | 支持        | 支持              | 不支持     |
| 跨注册中心同步  | 支持                           | 不支持      | 支持              | 不支持     |
| SpringCloud集成 | 支持                           | 支持        | 支持              | 不支持     |
| Dubbo集成       | 支持                           | 不支持      | 不支持            | 支持       |
| K8S集成         | 支持                           | 不支持      | 支持              | 不支       |

#### 动态配置服务    Spring Cloud Alibaba Nacos Config

##### Apollo 与 Nacos 功能对比  

- nacos配置文件支持比较多的格式，支持yaml、text、json、xml、html、Properties，apollo只支持xml、text、Properties的格式，没有兼容springboot中比较通用的yaml配置。
- apollo用户管理以及权限管理做的比较好和全面，适合做部门或者公司级的配置中心。nacos比较简洁明了（也可以说没有做权限这一块的开发），适合做小组内，或者小型java团体使用。
- apollo区分多环境是直接通过环境指定，可以直接对比和切换，而nacos是通过命名空间进行区分的。
- nacos是支持多格式的配置文件，但是解析上没有apollo做的好，apollo虽然支持的配置格式较少，不过会进行解析，使每个配置看起来比较直观，修改的时候比较直观，可以对单个进行修改。

##### Apollo与nacos对比结论

- Nacos部署简化，Nacos整合了注册中心、配置中心功能，且部署相比apollo简单，方便管理和监控。
- apollo容器化较困难，Nacos有官网的镜像可以直接部署，总体来说，Nacos比apollo更符合KISS原则
- 性能方面，Nacos读写tps比apollo稍强一些

#### 熔断与限流    Spring Cloud Alibaba Sentinel

