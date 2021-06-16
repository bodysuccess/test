---
title: redis简介
tags: redis
categories: 中间件
abbrlink: 1313dd35
date: 2021-04-29 21:42:01
---

### NoSQL简介

- NoSQL的全称     not only SQL
- 方便扩展，数据之间没有关系
- 大数据高性能（读取11万次每秒，写8万次每秒）
- 数据类型多种多样且不需要事先设计数据库的表，有更好的扩展性    

### 传统的数据库和NoSQL的区别

####   传统数据库：

- 结构化组织
- 数据都存储在表中
- 需要用sql进行查询
- 严格的一致性
- 数据定义语言

#### NoSQL：

- 不仅仅是数据库
- 没有固定的查询语言
- 键值对存储、列存储、文档存储、图形数据库等
- 最终一致性
- CAP定理和BASE理论
- 高性能、高可用、高扩展

### NoSQL的4种存储方式

- k-v键值对    常见的有redis、Tair、Memcache
- 文档数据库：MongoDB
- 列存储数据库：HBase、分布式文件系统
- 图形数据库：Neo4j

![nosql分类.png](https://i.loli.net/2021/04/29/RtrZoQSJaFqiGUs.png)



### Redis的概述

英文官网：    https://redis.io/

中文官网：    http://www.redis.cn/

#### redis是什么

Redis（Remote Dictionary Server )，即远程字典服务。

是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

#### redis能做什么

- 内存存储，持久化，常用的持久化是RDB、AOF
- 效率高用于缓存
- 发布订阅系统
- 地图信息分析
- 计时器、计数器
- ..................

#### redis的特性

- 多样的数据类型
- 持久化
- 集群
- 事务

#### redis数据库

redis有16个数据库，默认数据库是0号数据库

~~~shell
127.0.0.1:6379> select 3		#切换数据库到3
OK
127.0.0.1:6379[3]> dbsize		#显示当前数据库的key值数量
(integer) 0
127.0.0.1:6379[3]> set key 1	#设置一个值
OK
127.0.0.1:6379[3]> get key		#获取值
"1"
127.0.0.1:6379[3]> select 1	    #切换到1号数据库
OK
127.0.0.1:6379[1]> get key		#得到的数据为nil，也即是在3号数据库设置的值在1号数据库查不到
(nil)
127.0.0.1:6379[1]> keys *		#查询当前数据库的所有key值

127.0.0.1:6379[1]> FLUSHALL		#清空所有数据库的值
OK
127.0.0.1:6379[1]> FLUSHdb 		#清空当前数据库的值
OK

~~~

#### redis的原理

redis是单线程的，redis是基于内存操作的，CPU不是redis的性能瓶颈，redis的性能瓶颈是由内存和网络带宽决定的。

redis是用c语言编写的，每秒大概100000QPS，性能不比Memcache差。

由于redis的数据都存储在内存中，所以单线程操作更加快速。多线程操作会存在CPU之间的切换消耗资源。对于内存系统，没有CPU切换效率最高。