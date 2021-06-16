---
title: springSummary
tags: spring
categories: 总结
abbrlink: 75ca60a1
date: 2021-05-18 23:09:56
---

#### 什么是CAP理论

CAP原则又称CAP定理，指的是在一个分布式系统中，[一致性](https://baike.baidu.com/item/一致性/9840083)（Consistency）、[可用性](https://baike.baidu.com/item/可用性/109628)（Availability）、[分区容错性](https://baike.baidu.com/item/分区容错性/23734073)（Partition tolerance）。

CAP 原则指的是，这三个[要素](https://baike.baidu.com/item/要素/5261200)最多只能同时实现两点，不可能三者兼顾。一般保证容错性，一致性和可用性二选一。

一致性（C）：在[分布式系统](https://baike.baidu.com/item/分布式系统/4905336)中的所有数据备份，在同一时刻是否同样的值。（等同于所有节点访问同一份最新的数据副本）

可用性（A）：保证每个请求不管成功或者失败都有响应。

分区容忍性（P）：系统中任意信息的丢失或失败不会影响系统的继续运作。

#### HTTP和RPC的概述

Http是根据网络定义的，具有标准语义的通用接口，里面的资源都可被解释。对系统的可用性、伸缩性有更好的支持，而RPC是根据语言的API来定义的。

RPC主要用于公司内部的服务调用，性能消耗低，传输效率高，服务治理方便。HTTP主要用于对外的异构环境，浏览器接口调用，APP接口调用，第三方接口调用等。

#### HTTP和RPC的异同

- RPC，可以基于TCP协议，也可以基于HTTP协议
  HTTP，基于HTTP协议

- RPC，使用自定义的TCP协议，可以让请求报文体积更小，或者使用HTTP2协议，也可以很好的减少报文的体积，提高传输效率
  HTTP，如果是基于HTTP1.1的协议，请求中会包含很多无用的内容，如果是基于HTTP2.0，那么简单的封装一下是可以作为一个RPC来使用的，这时标准RPC框架更多的是服务治理性能消耗，主要在于序列化和反序列化的耗时

- RPC，可以基于thrift实现高效的二进制传输
  HTTP，大部分是通过json来实现的，字节大小和序列化耗时都比thrift要更消耗性能负载均衡

- RPC，基本都自带了负载均衡策略
  HTTP，需要配置Nginx，HAProxy来实现
  服务治理（下游服务新增，重启，下线时如何不影响上游调用者）

- RPC，能做到自动通知，不影响上游
  HTTP，需要事先通知，修改Nginx/HAProxy配置        

#### SpringCloud和Dubbo的区别

微服务的核心要素在于服务的发现、注册、路由、熔断、降级、分布式配置。

![微服务区别.jpg](https://i.loli.net/2021/05/18/izUmO8QhI1M63PF.jpg)

综合上述：dubbo属于早期的微服务框架，少了很多组件。而SpringCloud是一种生态，即把微服务架构所需要的组件都整合在一起使用起来更加方面。

dubbo用的是RPC进行通讯，而SpringCLoud用的是HTTP进行通讯。

