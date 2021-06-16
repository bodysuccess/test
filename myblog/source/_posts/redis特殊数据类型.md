---
title: redis特殊数据类型
tags: redis
categories: 中间件
abbrlink: 1e9fc50d
date: 2021-05-03 08:28:15
---

### geospatial地理位置

应用场景: 朋友圈定位、附近的距离、地理位置的计算

常用命令：

geoadd 添加经纬度信息

geopos 查询经纬度信息

geodist 查询两个位置直接的直线距离

geoRadius	查询某个坐标下（经度、维度）的所有位置信息

georadiusBymember	查询某个坐标下（集合内的数据）的位置信息

geohash	返回某个城市的一维数据，11位

~~~BASH
#顺序为key、经度、维度、城市
#有效的经度从-180度到180度。
#有效的纬度从-85.05112878度到85.05112878度。
#如果超过则会提示如下错误
127.0.0.1:6379> GEOADD china:city 116.405289 39.904987 beijing		#添加地理位置信息
(integer) 1
127.0.0.1:6379> GEOADD china:city 117.190186 39.125595 tianqing 114.502464  38.045475 shijiazhuang 126.642464 45.756966 haerbing 121.472641 31.231707 shanghai
(integer) 4
127.0.0.1:6379> GEOADD china:city 113.374375 23.368923 guangzhou
(integer) 1
127.0.0.1:6379> keys *
1) "china:city"
127.0.0.1:6379> GEOADD china:city    23.368923  113.374375  guangzhou		#位置信息添加错误提示
(error) ERR invalid longitude,latitude pair 23.368923,113.374375
127.0.0.1:6379> GEOPOS china:city beijing tianqing					#查看当前经纬度的值
1) 1) "116.40528827905654907"
   2) "39.90498588819134085"
2) 1) "117.19018846750259399"
   2) "39.12559461779084558"
#获取两人之间的距离
127.0.0.1:6379> GEODIST china:city beijing tianqing km				#获取两地之间的距离，单位为km
"109.7753"
#查询附近的人	key、经度、维度、半径、单位、直接距离、总数
127.0.0.1:6379> GEORADIUS china:city 100 30 1000 km withdist count 3		#查询某个经纬度内半径为1000km的地点3个位置
(empty array)
#查询以某个集合内的位置作为基础半径内的位置信息
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijing 500 km withdist
1) 1) "shijiazhuang"
   2) "264.2750"
2) 1) "tianqing"
   2) "109.7753"
3) 1) "beijing"
   2) "0.0000"
#获取某个城市的一维字符串
127.0.0.1:6379> GEOHASH china:city beijing
1) "wx4g0b7xru0"
127.0.0.1:6379> GEOHASH china:city shanghai guangzhou
1) "wtw3sjt9vs0"
2) "ws0sgyhty70"
~~~

> 地理位置的底层实现是根据zset进行操作的。

~~~BASH
127.0.0.1:6379> ZRAnge china:city 0 -1			#查询所有的经纬度信息
1) "guangzhou"
2) "shanghai"
3) "shijiazhuang"
4) "tianqing"
5) "beijing"
6) "haerbing"
127.0.0.1:6379> zrem china:city beijing			#删除某个经纬度信息
(integer) 1
127.0.0.1:6379> ZRAnge china:city 0 -1
1) "guangzhou"
2) "shanghai"
3) "shijiazhuang"
4) "tianqing"
5) "haerbing"
~~~

### Hyperloglog数据类型

优点：占用的内存是固定的，可以存取2的64个不同的元素，只需要12kb的内存。

缺点：是有0.81%的错误率，一般可以忽略不计。

目的：处理集合中不同的元素总数，跟set的用途一致。

- pfadd         添加元素
- pfcount      计算元素总数
- pfmegre    合并两个元素为一个全新的元素

~~~BASH
127.0.0.1:6379> PFADD key a b c d e f g h i j k			#向集合中添加元素
(integer) 1
127.0.0.1:6379> PFCOUNT key								#查看当前集合的元素总数	
(integer) 11
127.0.0.1:6379> PFADD key1 f f  n f e p o 				#重复元素的按1计算
(integer) 1
127.0.0.1:6379> PFCOUNT key1
(integer) 5
127.0.0.1:6379> PFMERGE key2 key key1					#合并两个key的元素为最新的key
OK
127.0.0.1:6379> PFCOUNT key2
(integer) 14
~~~

### BItMaps数据类型

表示位运算，一般应用场景是统计某个事情的真总数，以判断是否成立，例如：统计某员工一个月内的打卡次数

- setbit 	key   offset  0/1       	给某个key设置一个事件，用0表示假、1表示真
- getbit     key   offset                   获取这个key值的事件状态即0/1
- bitcount     key                            查询某个key的真事件的总数

~~~BASH
127.0.0.1:6379> setbit key 1 0						#设置某个元素的某种事件值是0或者1
(integer) 0
127.0.0.1:6379> setbit key 2 1
(integer) 0
127.0.0.1:6379> setbit key 2 0
(integer) 1
127.0.0.1:6379> getbit key 2						#获取该元素的事件状态
(integer) 0
127.0.0.1:6379> setbit key 3 0
(integer) 0
127.0.0.1:6379> BITCOUNT key						#获取某个元素的为1的时间总数
(integer) 0
127.0.0.1:6379> setbit key 3 1
(integer) 0
127.0.0.1:6379> BITCOUNT key
(integer) 1

~~~

