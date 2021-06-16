---
title: redis进阶
tags: redis
categories: 中间件
abbrlink: d6c4a96f
date: 2021-05-03 10:16:21
---

### Redis事务

Redis事务本质是一种命令的集合，一个事务中的所有命令都会被序列化，且按照顺序执行！

Redis事务没有隔离级别的概念，即没有mysql的脏读等事情发生

Redis的单条命令是具有原子性的，多条命令不具有原子性

- 开启事务（multi）
- 命令入队（。。。。。）
- 执行事务（exec）

~~~bash
#一个完整的事务执行过程
127.0.0.1:6379> MULTI						#开启一个事务
OK
127.0.0.1:6379(TX)> set k1 v1				#在事务中执行操作
QUEUED
127.0.0.1:6379(TX)> set k2 v2 
QUEUED
127.0.0.1:6379(TX)> get k2
QUEUED
127.0.0.1:6379(TX)> exec					#执行事务
1) OK
2) OK
3) "v2"
~~~

~~~BASH
#如果在事务中做了取消操作，则所有操作都不会执行，此时具有原子性
127.0.0.1:6379> MULTI					#开启事务
OK
127.0.0.1:6379(TX)> set k1 v1 			#执行操作
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> DISCARD				#取消事务
OK
127.0.0.1:6379> get k1					
(nil)
~~~

~~~bash
#编译时异常，即系统检测出的异常，此时具有原子性
27.0.0.1:6379> MULTI					#开启事务
OK
127.0.0.1:6379(TX)> set k1 v1			#执行正确操作
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> getset k1			#执行错误操作
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379(TX)> get k2
QUEUED
127.0.0.1:6379(TX)> exec 				#执行事务时提示所有操作都未成功
(error) EXECABORT Transaction discarded because of previous errors.
~~~

~~~bash
#运行时异常，即系统运行过程中才检测到异常，此时不具有原子性
127.0.0.1:6379> MULTI					#开启事务
OK
127.0.0.1:6379(TX)> set k1 v1			#执行正确操作
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> incr k1				#执行错误操作，向String中自增
QUEUED
127.0.0.1:6379(TX)> get k2
QUEUED
127.0.0.1:6379(TX)> exec				#执行事务，则提示第三步错误，其他都正常运行
1) OK
2) OK
3) (error) ERR value is not an integer or out of range
4) "v2"
~~~

### redis的监控

> 乐观锁是什么

无论什么时候都认为不会出现问题，所以不用上锁，更新数据的适合判断一下是否有人修改过数据即可。

> 悲观锁是什么

无论什么时候都认为会出现问题，所以执行过程中都会增加一个锁。效率过慢

> redis的乐观锁用watch进行表示

~~~bash
#一个正常的操作
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money				#开启监控
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> decrby money 60		#减少60
QUEUED
127.0.0.1:6379(TX)> INCRBY out 60		#增加60
QUEUED
127.0.0.1:6379(TX)> exec
1) (integer) 40
2) (integer) 60
~~~

~~~BASH
#一个错误信息的执行过程

#线程1
127.0.0.1:6379> WATCH money				#监控money
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> DECRby money 10		#对money进行操作
QUEUED
127.0.0.1:6379(TX)> INCRBY out 10
QUEUED
127.0.0.1:6379(TX)> exec 				#执行事务不成功
(nil)
#线程2
127.0.0.1:6379> set money 1000			#线程2修改了监控的money
OK


127.0.0.1:6379> UNWATCH					#取消所有监控信息
OK
~~~

### 设置redis的密码

~~~bash
127.0.0.1:6379> config set requirepass 123456			#设置redis的密码
OK
127.0.0.1:6379> exit
root@5a78b143c447:/data# redis-cli
127.0.0.1:6379> ping
(error) NOAUTH Authentication required.
127.0.0.1:6379> auth 123456							#密码认证
OK
127.0.0.1:6379> ping
PONG
~~~

### RDB（redis database）持久化

> 触发机制产生dump.rdb文件

- 配置文件中设置的save规则如果满足条件即可触发，例如save 900 1 （如果900s内存在一次更改或添加操作）
- 执行flushAll命令，也会触发持久化配置产生rdb文件
- 退出redis也可产生rdb文件。

> 如何恢复rdb文件

找到rdb文件需识别的目录，启动redis的适合会自动加载

~~~bash
127.0.0.1:6379> config get dir			#输出此命令可查看rdb文件所在目录
1) "dir"
2) "/etc"
~~~

**优点**

1. 适合大规模的数据恢复
2. 对数据的完整性不高

**缺点**

1. 需要一定的时间间隔进程操作，如果redis意外down了，则最后一次修改的数据就持久化不成功。
2. fork进行的时候，暂用一定的内存空间。

### AOF（append only file）持久化

由于redis的默认持久化方式为RDB，需修改配置文件中的APPENDONLY 为YES则表示AOF持久化

AOF持久化产生的文件名为appendOnly.aof文件

AOF持久化的方式有三种：

- appendfSync    always		每次修改都会sync，比较消耗性能
- appendfSync    everysec    每秒执行一次sync，可能会丢失这1s的数据，默认的方式
- appendfSync    no               不执行sync，操作系统回自己同步数据，速度最快

**优缺点**

1. 相对于rdb的方式，数据的完整性更好
2. 相对于rdb的方式，aof的文件远大于rdb文件，修复速度也比rdb慢
3. 运行效率也比rdb慢

### Redis消息队列

~~~BASH
# 消息接收者
127.0.0.1:6379> SUBSCRIBE wei 					#设置接收数据的频道wei
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "wei"
3) (integer) 1
1) "message"									#下三步则为接收到数据信息
2) "wei"
3) "123232"

#消息发送者
127.0.0.1:6379> PUBLISH wei 123232				#发送到wei频道信息
(integer) 1
~~~

### Redis的主从复制

> 主从复制的概念

主从复制试讲一台Redis服务器复制到其他服务器，前者称为主节点（master/leader),后者称为从节点（slave/follower）;数据的复制是单向的，只能从主节点到从节点，其中master为主，slave为从。

> 主从复制的作用是什么

1. 数据冗余：实现了数据的热备份，是持久化之外的一种数据冗余模式
2. 故障恢复：当主节点出现问题时，可以有从节点提供服务，实现快速的故障恢复，实际上是一种服务的冗余。
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，从节点提供读服务，分担服务器负载。
4. 高可用：除了上述作用外，主从复制还采用了哨兵和集群模式，实现了redis的高可用。

~~~BASH
127.0.0.1:6379> INFO replication			#查看当前redis的机器信息
# Replication
role:master				#角色信息（主机）
connected_slaves:0		#从机数量
~~~

~~~bash
127.0.0.1:6379> info replication
# Replication
role:master		#主机标识
connected_slaves:1		#链接的从机数目
slave0:ip=47.101.169.95,port=6379,state=online,offset=14,lag=0		#从机的基本信息

127.0.0.1:6379> info replication			
# Replication
role:slave				#从机		
master_host:47.100.3.247		#连接到的主机信息
master_port:6379

~~~

### 哨兵模式

> 哨兵模式的概念

主从切换技术的方法是：当主服务器down后，需要手动选择一台服务器作为主服务器，需要人工干预，费时费力，并且还会造成一段时间的不可用，因此产生一个全新的自动化模式sentinel来解决这个问题。如果主机down，从机则会根据一定的算法选择从机做为主机。如果主机在连接则只能作为从机，主机还是原来推举出来的。

> 哨兵的作用

- 通过发送命令，检测主服务器和从服务器的运行状态
- 当master宕机，会自动将slave转换为master，然后通知其他服务器，修改配置文件，让他们切换主机。
- 哨兵也可能存在down的可能，因此会布置多个哨兵互相监控。

> 哨兵配置

1、配置sentinel.conf

~~~bash
# sentinel monitor myredis host port  1   
sentinel monitor myredis 47.100.3.247 6379  1		#1表示主机挂了，slave投票谁最多谁成为主机
#具体的配置后续再了解，目前只配置最基础的配置
~~~

2、启动哨兵

~~~cmd
redis-sentinel sentinel.conf
~~~

**优点：**

1. 哨兵集群，基于主从复制模式，所有的主从配置有点都拥有
2. 主从可以切换，故障可以转移，系统的可用性会更好
3. 哨兵模式是主从模式的升级，手动改为自动更加的健壮

**缺点：**

1. 哨兵模式的扩容非常麻烦
2. 配置相对来说比较麻烦