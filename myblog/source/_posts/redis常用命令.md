---
title: redis常用命令
tags: redis
categories: 中间件
abbrlink: c3982ec0
date: 2021-04-29 21:42:36
---

### redis的命令查找网址

http://www.redis.cn/commands.html

### redis的基本命令

~~~SHELL
127.0.0.1:6379> FLUSHALL			#清空所有数据库
OK
127.0.0.1:6379> set name '123'		#设置key为name的值
OK	
127.0.0.1:6379> set age 1			#设置key为age的值
OK
127.0.0.1:6379> get name			#获取key为name的值，如果不存在key值则返回nil。
"123"
127.0.0.1:6379> gea age
(error) ERR unknown command `gea`, with args beginning with: `age`, 
127.0.0.1:6379> get age				#获取key为age的值
"1"
127.0.0.1:6379> EXISTS naem			#查询是否存在此key值，存在返回1，否则返回0
(integer) 0
127.0.0.1:6379> EXISTS name			
(integer) 1
127.0.0.1:6379> move name 1			#移除key值，成功返回1，失败返回0
(integer) 1
127.0.0.1:6379> keys *				#查询当前存在的key值
1) "age"
127.0.0.1:6379> EXPIRE age 15		#设置key值得过期时间
(integer) 1
127.0.0.1:6379> ttl name			
(integer) -2
127.0.0.1:6379> ttl age				#查看当前key的剩余时间，如果key值不存在则返回-2
(integer) 7
127.0.0.1:6379> ttl age
(integer) 4
127.0.0.1:6379> ttl age
(integer) -2
127.0.0.1:6379> type age			#查看key值得返回类型
String
~~~

### String基本数据类型

~~~SHELl
127.0.0.1:6379> FLUSHALL
OK
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> set key1 1
OK
127.0.0.1:6379> get key1
"1"
127.0.0.1:6379> EXISTS key1
(integer) 1
127.0.0.1:6379> EXISTS key1
(integer) 1
127.0.0.1:6379> EXISTS key2
(integer) 0
127.0.0.1:6379> APPEND key1 2		#给某个key追加一个值，如果当前值不存在则创建当前key，返回的当前value的长度
(integer) 2
127.0.0.1:6379> keys *
1) "key1"
127.0.0.1:6379> get key1
"12"
127.0.0.1:6379> STRLEN key1			#查看当前key的长度
(integer) 2
127.0.0.1:6379> append key1 '321321'
(integer) 8
127.0.0.1:6379> STRLEN key1
(integer) 8
127.0.0.1:6379> get key1
"12321321"
~~~

~~~shell
127.0.0.1:6379> set view 0
OK
127.0.0.1:6379> get view
"0"
127.0.0.1:6379> incr view			#给某个字段字段增加1
(integer) 1
127.0.0.1:6379> incr view	
(integer) 2
127.0.0.1:6379> incr view
(integer) 3
127.0.0.1:6379> decr view			#给某个字段自动减少1
(integer) 2
127.0.0.1:6379> INCRBY view 10		#给某个字段增加10
(integer) 12
127.0.0.1:6379> INCRBY view 10		
(integer) 22
127.0.0.1:6379> decrby view 5		#给某个字段减少5
(integer) 17
127.0.0.1:6379> incr key1			#如果自增的数据不是Integer类型则提示错误
(error) ERR value is not an integer or out of range
~~~

~~~SHELL
127.0.0.1:6379> set key1 abcde
OK
127.0.0.1:6379> get key1
"abcde"
127.0.0.1:6379> GETRANGE key1 0 3		#获取下表0-3的数据
"abcd"
127.0.0.1:6379> GETRANGE key1 0 -1 		#获取全部的数据
"abcde"
127.0.0.1:6379> SETRANGE key1 0 111		#从下表为0开始替换数据，长度为值得长度
(integer) 5
127.0.0.1:6379> get key1 
"111de"
~~~

~~~shell
127.0.0.1:6379> clear
127.0.0.1:6379> keys *
1) "key1"
127.0.0.1:6379> get key1
"111de"
127.0.0.1:6379> SETNX key1 'abcde'			#设置某个值如果存在则不替换，不存在则添加
(integer) 0
127.0.0.1:6379> get key1
"111de"
127.0.0.1:6379> SETEX key1 30 abcde			#设置某个值存在即替换，不存在则添加，且设置其过去时间为30s.
OK
127.0.0.1:6379> get key1
"abcde"
127.0.0.1:6379> ttl key1					#查看剩余时间，如果过期或者不存在则返回0
(integer) 22
127.0.0.1:6379> ttl key1
(integer) 0
127.0.0.1:6379> keys *
(empty array)
~~~

~~~shell
127.0.0.1:6379> FLUSHdb
OK
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3			#设置多个key/value值
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"
127.0.0.1:6379> mset k1 v1 k4 v4				#设置多个，如果存在则替换，不存在则添加
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"
4) "k4"
127.0.0.1:6379> MSETNX k1 v1 k5 v5				#设置多个，如果存在则全部失败，因为redis具有原子性。
(integer) 0
127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"
4) "k4"
127.0.0.1:6379> get k5
(nil)
127.0.0.1:6379> GETSET db redis					#获取key值并设置key值，返回值为实现get的key值
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mysql
"redis"
127.0.0.1:6379> get db
"mysql"
~~~

### List基本数据类型

~~~SHELL
127.0.0.1:6379> FLUSHdb
OK
127.0.0.1:6379> keys *					
(empty array)
127.0.0.1:6379> LPUSH list a b c			#从左侧插入一直list，返回的是当前集合的长度
(integer) 3
127.0.0.1:6379> lpush list d e				
(integer) 5
127.0.0.1:6379> RPUSH list f g				#从右侧插入一个list
(integer) 7
127.0.0.1:6379> LRANGE list 0 -1 			#获取list的所有值
1) "e"
2) "d"
3) "c"
4) "b"
5) "a"
6) "f"
7) "g"
127.0.0.1:6379> LRANGE list 1  5			#获取list下标为1-5的值
1) "d"
2) "c"
3) "b"
4) "a"
5) "f"
127.0.0.1:6379> RPOP list 					#移除右边的第一个元素
"g"
127.0.0.1:6379> RPOP list 2					#移除右边的2个元素
1) "f"
2) "a"
127.0.0.1:6379> LPOP list 2					#移除左边边的2个元素
1) "b"
2) "c"
127.0.0.1:6379> LRANGE list 0 -1
1) "e"
2) "d"
127.0.0.1:6379> LINDEX list 1				#获取当前集合下标为1的元素
"d"
127.0.0.1:6379> LLEN list					#获取list集合的长度
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1			
1) "e"
2) "d"
127.0.0.1:6379> LPUSH list e				#王list中添加一个重复元素
(integer) 3
127.0.0.1:6379> LREM list 2 e				#删除list中为e的元素，且值的个数为2
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1		
1) "d"
127.0.0.1:6379> FLUSHdb 
OK
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> clear
127.0.0.1:6379> lpush list hello1
(integer) 1
127.0.0.1:6379> lpush list hello2
(integer) 2
127.0.0.1:6379> lpush list hello2
(integer) 3
127.0.0.1:6379> lpush list hello3
(integer) 4
127.0.0.1:6379> lpush list hello4
(integer) 5
127.0.0.1:6379> LREM list 1 hello 2			
(error) ERR wrong number of arguments for 'lrem' command
127.0.0.1:6379> LREM list 1 hello2		
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "hello4"
2) "hello3"
3) "hello2"
4) "hello1"
127.0.0.1:6379> LPUSH list hello5
(integer) 5
127.0.0.1:6379> LRANGE list 0 -1
1) "hello5"
2) "hello4"
3) "hello3"
4) "hello2"
5) "hello1"
127.0.0.1:6379> LTRIM list 1 2				#截取当前key的下标为1到2的值
OK
127.0.0.1:6379> LRANGE list 0 -1
1) "hello4"
2) "hello3"
127.0.0.1:6379> RPOPLPUSH list mylist		#截取list右侧第一个元素把他存入到mylist的最左侧
"hello3"
127.0.0.1:6379> LRANGE list 0 -1			#查看list的所有值
1) "hello4"
127.0.0.1:6379> LRANGE mylist 0 -1			#查看mylist的所有值
1) "hello3"
127.0.0.1:6379> FLUSHdb
OK
127.0.0.1:6379> EXISTS list					#查看是否存在当前key值	
(integer) 0
127.0.0.1:6379> lset list 0 redis			#往当前key值中set一个下标为0的数据，如果不存在则提示错误，存在则替换
(error) ERR no such key
127.0.0.1:6379> LPUSH list mysql
(integer) 1
127.0.0.1:6379> lset list 0 redis		
OK
127.0.0.1:6379> LRANGE list 0 -1
1) "redis"
127.0.0.1:6379> lset list 1 redis
(error) ERR index out of range
##############################################
127.0.0.1:6379> LRANGE list 0 -1
1) "redis"
127.0.0.1:6379> lpush list myql			
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "myql"
2) "redis"
127.0.0.1:6379> LINSERT list before 'mysql' mysql1
(integer) -1
127.0.0.1:6379> LRANGE list 0 -1
1) "myql"
2) "redis"
127.0.0.1:6379> LINSERT list after 'mysql' mysql1
(integer) -1
127.0.0.1:6379> LRANGE list 0 -1
1) "myql"
2) "redis"
127.0.0.1:6379> keys *
1) "list"
127.0.0.1:6379> LRANGE list 0 -1
1) "myql"
2) "redis"
127.0.0.1:6379> LINSERT list after "redis" "mysql1"			#往某个key得之前或之后插入一个值，值不能是下标为0的数据。
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1
1) "myql"
2) "redis"
3) "mysql1"
127.0.0.1:6379> LINSERT list before "redis" "mysql1"
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "myql"
2) "mysql1"
3) "redis"
4) "mysql1"
127.0.0.1:6379> LINSERT list before "mysql" "mysql1"
(integer) -1
127.0.0.1:6379> LINSERT list after "mysql" "mysql1"
(integer) -1
~~~

### Set基本数据类型

~~~SHELL
127.0.0.1:6379> sadd key hello				#向key值添加一个元素，如果存在，则返回0，如果不存在则返回1
(integer) 1
127.0.0.1:6379> sadd key world
(integer) 1
127.0.0.1:6379> sadd key world
(integer) 0
127.0.0.1:6379> SMEMBERS key				#查看key的所有元素
1) "world"
2) "hello"
127.0.0.1:6379> SISMEMBER key hellp			#查看当前key是否存在某个值，如果存在则返回1，不存在则返回0
(integer) 0
127.0.0.1:6379> SISMEMBER key hello
(integer) 1
127.0.0.1:6379> scard key					#查看当前key的所有值
(integer) 2
127.0.0.1:6379> SREM key hello				#移除某个元素，如果原有元素存在则返回1，原有元素不存在则返回0
(integer) 1
27.0.0.1:6379> SMEMBERS key
1) "world"
127.0.0.1:6379> Sadd key 1
(integer) 1
127.0.0.1:6379> Sadd key 2
(integer) 1
127.0.0.1:6379> Sadd key 3
(integer) 1
127.0.0.1:6379> SMEMBERS key
1) "3"
2) "1"
3) "world"
4) "2"
127.0.0.1:6379> SRANDMEMBER key				#随机抽取一个key值
"world"
127.0.0.1:6379> SRANDMEMBER key
"world"
127.0.0.1:6379> SRANDMEMBER key
"1"
127.0.0.1:6379> SRANDMEMBER key
"2"
127.0.0.1:6379> SRANDMEMBER key
"world"
127.0.0.1:6379> SRANDMEMBER key
"3"
127.0.0.1:6379> SRANDMEMBER key 2			#随机抽取指定个数key值
1) "3"
2) "2"
127.0.0.1:6379> SPOP key					#随机删除一个元素，也可跟个数
"2"
127.0.0.1:6379> SPOP key
"1"
127.0.0.1:6379> SMEMBERS key
1) "3"
2) "world"
127.0.0.1:6379> FLUSHdb
OK
127.0.0.1:6379> sadd key 1
(integer) 1
127.0.0.1:6379> sadd key 2 3 4 5
(integer) 4
127.0.0.1:6379> SMOVE key key1 2			#把某个key的值移动另外一个key中
(integer) 1
127.0.0.1:6379> keys *
1) "key"
2) "key1"
127.0.0.1:6379> SMEMBERS key
1) "1"
2) "3"
3) "4"
4) "5"
127.0.0.1:6379> SMEMBERS key1
1) "2"
127.0.0.1:6379> sadd key1 1 3 4
(integer) 3
127.0.0.1:6379> SMEMBERS key				#查询key的value值
1) "1"
2) "3"
3) "4"
4) "5"
127.0.0.1:6379> SMEMBERS key1
1) "1"
2) "2"
3) "3"
4) "4"
127.0.0.1:6379> sdiff key key1				#以key为基准，求两个key的差集
1) "5"
127.0.0.1:6379> SINTER key key1				#求两个集合的交集
1) "1"
2) "3"
3) "4"
127.0.0.1:6379> SUNION key key1				#求两个集合的并集
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
~~~

### Hash的基本命令（key-(key-value)

~~~SHELL
127.0.0.1:6379> hset hash key 1							#向某个key值中添加一个key/value键值对 其中外层key为hash,内层key为key，value为1
(integer) 1
127.0.0.1:6379> hset hash key 1 key1 2 key2 3			#向key值hash中添加多个元素
(integer) 2
127.0.0.1:6379> HGET hash key							#获取hash中的key的值
"1"
127.0.0.1:6379> HMGET hash key key1 key2				#获取多个值
1) "1"
2) "2"
3) "3"
127.0.0.1:6379> HGETALL hash							#获取hash的所有key、value信息
1) "key"
2) "1"
3) "key1"
4) "2"
5) "key2"
6) "3"
127.0.0.1:6379> HDEL hash key key1						#删除hash的key和key1的值
(integer) 2
127.0.0.1:6379> HGETALL hash
1) "key2"
2) "3"
127.0.0.1:6379> HLEN hash								#获取hash的键值对总数
(integer) 1
127.0.0.1:6379> HEXISTS hash key1						#查看hash中是否存在某个key值，如果存在返回1，如果不存在返回0
(integer) 0
127.0.0.1:6379> HGETALL hash
1) "key2"
2) "3"
127.0.0.1:6379> HEXISTS hash key2
(integer) 1
127.0.0.1:6379> HKEYS hash								#获取hash里面所有的key值
1) "key2"
127.0.0.1:6379> HVALS hash								#获取hash中所有value值
1) "3"
127.0.0.1:6379> hset hash key3 5
(integer) 1
127.0.0.1:6379> HKEYS hash
1) "key2"
2) "key3"
127.0.0.1:6379> HVALS hash
1) "3"
2) "5"
127.0.0.1:6379> HINCRBY hash key3 5						#给hash的key3值增加5
(integer) 10
127.0.0.1:6379> HINCRBY hash key3 -1					#给hash的key3值增加-1
(integer) 9
127.0.0.1:6379> HSETNX hash key3 8						#如果存在则给某个key添加至，不存在则返回0.
(integer) 0
~~~

### Hset的基础类型

~~~SHELL
127.0.0.1:6379> zadd salary 800 lk						#向某个元素添加至，其中key为salary,800为位置，即排序的标准
(integer) 1
127.0.0.1:6379> zadd salary 900 wl 1000 ml
(integer) 2
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf			#查看所有的value值，
1) "lk"
2) "wl"
3) "ml"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 900 withscores 	#查看小于900的value值，显示score
1) "lk"
2) "800"
3) "wl"
4) "900"
127.0.0.1:6379> ZRAnge salary 0 -1					#查询所有key值，无排序，按插入顺序
1) "lk"
2) "wl"
3) "ml"	
127.0.0.1:6379> ZRem salary lw						#移除某个元素，如果存在返回1，不存在返回0
(integer) 0
127.0.0.1:6379> ZRem salary lk
(integer) 1
127.0.0.1:6379> ZRAnge salary 0 -1
1) "wl"
2) "ml"
127.0.0.1:6379> ZCARD salary						#获取集合中的个数
(integer) 2
127.0.0.1:6379> ZREVRANGE salary 0 -1 withscores	#按socre从大到小排序
1) "ml"
2) "1000"
3) "wl"
4) "900"
127.0.0.1:6379> ZCOUNT salary 0 900					#获取集合内的总数
(integer) 1
~~~

