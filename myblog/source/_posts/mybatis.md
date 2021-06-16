---
title: mybatis
tags: mybatis
categories: 中间件
abbrlink: 15832afd
date: 2021-05-13 20:16:25
---

#### mybatis的中文官网地址

[https://mybatis.org/mybatis-3/zh/](https://mybatis.org/mybatis-3/zh/)

#### mybatis的主要核心部件

- SqlSession    表示和数据库交互的会话，完成必要的数据库增删改查功能
- Executor       执行器，是mybatis的核心，负责sql语句的执行，缓存的查询
- StatementHandler    封装了JDBC Statement操作，负责对JDBC statement的操作，如设置参数，或将结果集转换成List
- ParameterHandler    负责对用户传递的参数转换成JDBC Statement所需要的参数
- ResultSetHandler      负责将jdbc返回的结果集转换成List集合
- TypeHandler              负责将java的数据类型和jdbc数据类型之间的转换
- Configuration             配置mybatis的信息
- SqlSource                    负责用户传递的paramterObject,动态的生成SQL语句，将信息封装到BoundSql中
- BoundSql                    表示动态生成SQL语句以及相应的参数信息

#### mybatis 工作流程

1. 读取mybatis的配置信息，可以是xml的格式，也可以是基于Configuration的对象。
2. 实例化SqlSessionFactoryBuilder,并通过build配置文件生成SqlSessionFactory实例
3. 创建一个SqlSession会话，用于实现CRUD。
4. 创建会话时也会创建一个新的Executor执行器，用于执行SQL或者查询缓存
5. 执行完成，返回结果并关闭SqlSession会话。

#### mybatis的缓存

##### 一级缓存SqlSession

mybatis的一级缓存是默认开启的，是SqlSession级别的，即拿到这个链接到结束连接的过程中缓存是有效的。其特点有以下：

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。

```xml
<!--可以设置缓存算法，刷新间隔、引用大小、只读表示等 -->
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

##### 二级缓存nameSpace

mybatis的二级缓存也叫全局缓存，由于一级缓存的作用域只在sqlSession中（太窄了），所以诞生了二级缓存，它是基于nameSpace级别的缓存，相当于一个nameSpace对应一个二级缓存。mybatis的二级缓存需要手动开启。

二级缓存的工作机制：

- 当一个会话查询到一条数据时首先会存储到一级缓存中
- 如果当前会话关闭，则一级缓存不存在了，则会把一级缓存的信息过渡到二级缓存
- 当我们再次在此nameSPace空间内查询时则会从二级缓存中获得内容
- 不同的nameSpace有不同的缓存集合

##### 自定义缓存

自定义缓存只需实现Cache就可以了！具体可查看官方文档！