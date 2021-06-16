---
title: docker基础
tags: docker
categories:
  - 中间件
abbrlink: 5ae6092
date: 2021-04-18 12:49:37
---

###### docker的默认工作路径是      /var/lib/docker

###### docker的基础是镜像与容器

###### docker的官网地址：https://docs.docker.com/ 	

#### Docker的基本命令：

```
docker version    	#查看docker的基本信息
docker info 		#显示docker的系统信息，包括镜像和容器镜像
docker 命令 --help 		#docker命令帮助查询
12321312
```



#### Docker的镜像常用命令：

~~~shell
docker images -a		#查看所有镜像信息
docker images -q 		#查看所有镜像id
docker images -filter ......   #过滤信息
~~~

#### docker搜索镜像：

~~~~shell
docker search 服务名称  
例如：docker search java 
	 docker search mysql
docker search mysql --filter=stars=200   #查询星数在200以上
~~~~



#### 拉取镜像：

~~~shell
docker pull (镜像名称)：（镜像版本）   #例如  docker pull mysql:5.7   
docker pull (镜像名称)				#拉取最新版本的镜像信息
~~~

#### 删除镜像：

~~~shell
docker rmi -f (镜像id)		#删除镜像信息，镜像d可通过docker images进行查询
docker rmi -f $（dokcer images）		 #删除所有镜像信息
dokker rmi -f 镜像id 镜像id 			#删除多个镜像信息
~~~

#### Docker的容器命令：

###### 首先我们先下载个centos镜像命令进行学习

~~~
docker pull centos
~~~

###### 容器命令：

~~~shell
docker run [可选参数] image
#常用参数
--name   #容器名字，用来区分容器
-d		 #后台方式运行
-it 	 #使用交互方式运行，进入容器查看内容
-p		 #指定容器的端口  -p 主机端口：容器端口
-P		 #随机指定端口

运行查看容器：
docker run -it centos /bin/bash  		#进入centos
docker ps 				#查看正在运行的容器
docker ps -a 			#查看历史运行的容器
docker ps -n=？		   #查看最近运行的容器,如果加上=？则表示最近几条

退出容器：
exit 			#容器停止并退出
CTRL+P+Q		#容器不停止退出

删除容器：
docker rm 容器id    		#删除非运行的容器，如果运行会报错
docker rm -f 容器id		#强制删除容器
docker rm -f $(docker ps -aq)		#删除所有容器id
~~~

###### 启动容器命令：

~~~shell
docker start 容器id			#启动容器
docker restart 容器id			#重启容器
docker stop 容器id			#停止正在运行的容器
docker kill 容器id			#强制停止当前容器
~~~

###### 日志命令：

~~~shell
docker logs -tf tail 10 容器id   #查询后十条日志信息   tail 10 可省略
~~~



###### 常用其他命令：

~~~shell
docker top 容器id			#查看容器进程
docker inspect  容器id	#查看容器的基本信息
docker exec  -it  容器id /bin/bash		#进入正在启动的容器，并开启一个新容器
docker attach 容器id						#进入正在启动的容器，不会启动新的容器
docker cp 容器id:容器内路径 容器外路径		  #复制文件从内部容器到外部容器
docker stats 			#查询docker的内存使用率
~~~

###### 下载nginx镜像到linux服务器

~~~shell
docker search nginx 		#查看镜像
docker pull nginx 			#下载最新版本的镜像
docker images				#查看下载的容器
docker run -d --name nginx01 -p 3344:80 nginx		
#-d   后台运行
# --name 给容器起个名字以便分辨
# -p 3344 宿主机端口,即外部端口
# 80 内部容器端口即nginx端口 
~~~

###### 下载mysql5.7到linux服务器

```shell
#获取mysql镜像
docker pull mysql:5.7
# -v 卷挂载
# -e 环境配置
# -d 后台运行
# -p 端口映射
# --name 给容器命名
# 配置mysql地址环境
docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=wlk161536 --name mysql01 mysql:5.7

#-v的目的是映射到本地 前面的表示宿主机文件，后面表示docker内文件

```

###### 提交镜像

~~~shell
docker commit -m="描述提交的信息" -a="作者" 容器id 目录镜像名:[tags版本]

例如：
1、先运行一个tomcat镜像
docker run -it 容器id /bin/bash
2、启动tomcat镜像
docker exec -it 容器id /bin/bash
3、由于docker中的webApps下没有目录，需要webApps.dist文件复制到webApps下
cp -r webApps.dist/* webApps
4、把修改后的镜像提交
docker commit -m ="添加webbApps信息" -a="moerWolld" 容器id tomcat2:1.0
~~~

~~~SHELL
#查看镜像的历史构建记录   可查看构造方式仿造一个同样的镜像
docker history 镜像id	
~~~

```

```
