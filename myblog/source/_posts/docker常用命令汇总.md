---
title: docker常用命令汇总
tags: docker
categories: 中间件
abbrlink: a2145400
date: 2021-04-28 23:36:29
---

#### 普通指令

~~~bash
# 启动docker
systemctl start docker 
#停止docker
systemctl stop docker
#重启docker
systemctl restart docker
#查看版本
docker version
#查看docker信息
docker info
#帮助
docker --help
~~~

#### 镜像命令

~~~SHELL
# 查看已安装的镜像
docker images
# 查询镜像
docker search 镜像名   #例如：docker search tomcat
# 拉取镜像
docker pull 镜像名		#例如：docker pull tomcat:8.5 如果后面不跟版本后则默认下载最新版本
# 删除镜像
docker rmi 镜像名		#例如 docker rmi tomcat
docker rmi -f  镜像id		#镜像id是创建镜像的唯一表示
# 删除所有镜像
docker rmi -f ${docker images -qa}  #即查询出所有镜像并删除
# 打包一个镜像
docker commit -a 作者信息 -m="要提交的信息" 镜像id  要生产的镜像名称
# 运行一个镜像,运行成功之后则生成一个容器
docker run -it -p 外部端口号:内部端口号 镜像名称  
~~~

#### 容器命令

~~~SHELL
# 通过镜像生成容器的常用命令
--name  	#给容器起一个名字
-d			#表示后台运行
-it			#表示进入控制台运行与-d功能相同
-P			#随机分配一个端口号不常用
-p			#端口映射	外部端口：内部端口
-v			#卷挂载	宿主机路径：内部路径   用途是挂载文件是同步的，即可通过修改外部文件已到达修改内部文件的目的
# 查询docker正在运行的容器
docker ps   	#后续的常用参数
-a 			#查看所有容器
-l			#最近创建的容器
-n			#最近创建的n个容器
# 退出容器
exit 		#退出并停止
ctrl+p+Q	#退出但不停止
# 启动、停止、重启、杀死容器
docker start/stop/restart/kill 容器id或容器名
# 删除容器   
docker rm 容器id或容器名		#只能删除已停止的容器
docker rm -f  容器id或容器名	#强制删除容器
docker rm -f ${docker ps -qa}  #删除所有容器
#重新进入容器
docker attach 容器id或容器名		#这种方式太慢
docker exex -it 容器id或容器名 /bin/bash 	#进入正在执行的容器并进行交互
# 拷贝文件从内部到外部
docker cp 容器id或容器名:文件路径 宿主机路径
~~~

#### 日志命令

~~~SHELL
 docker logs -f -t --tail 10   容器id或容器名
 -t    		#表示显示时间
 -f			#最新的日志打印
 --tail 	#行数
 -n			#全部打印
 
 docker top 容器id或容器名    #查看容器内的进程
~~~

