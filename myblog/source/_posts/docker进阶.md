---
title: docker进阶
tags: docker
categories: 中间件
abbrlink: abdfa13a
date: 2021-04-21 20:23:13
---

#### 容器卷技术

容器卷技术的目的是为了持久化和同步操作！容器件也可以进行同步。

~~~SHELL
#容器与本地数据挂载
# 一、命令挂载
docker run -it -v  linux文件目录:容器目录 容器id /bin/bash	#把linux目录和容器目录进行同步
# 二、dockerFile 挂载
# 1、建立dockerFile文件  其中书写指令是必须都大写
FROM centos		#要挂载的镜像
VOLUME ["volume01","volume2"]	#要挂载的卷
CMD echo "----end----"
CMD /bin/bash
# 2、运行文件
docker build -f 创建的dockerFile目录 -t 生成的名字

#容器之间挂载 把两容器关联起来
docker run -it --name 新建容器 --volume-from 要挂载的容器  moer/centos:1.0
~~~

#### Dockerfile构建过程

###### 基础知识

1、每个指令必须的大写

2、指令的执行顺序从上到下

3、#表示注释

4、每个指令都会提交一个镜像，层层提交。

#### DokcerFile的基础命令

![docker命令.jpg](https://i.loli.net/2021/04/22/9BiAIRS7wEUqkPD.jpg)

~~~shell
FROM 				#基础镜像
MAINRAINER			#姓名+邮箱
RUN					#运行命令
ADD					#添加分层镜像
WORKDIR				#工作目录
VOLUME				#挂载卷
EXPOSE				#暴露端口号
CMD					#指定容器运行的适合要启动的命令，只有最后一个命令生效，不可追加
ENTRYPOINT			#指定容器运行的适合要启动的命令，可以追加命令
ENV					#构建的时候设置环境变量
COPY				#把一个镜像拷贝进去类似ADD
~~~

###### docker 网络

~~~SHELL
docker network ls   #查看所有的docker网络
b6fbe2727cc1        bridge              bridge              local
e8ea254fb069        host                host                local
4956a8e7920a        none                null                local
#其中bridge 是桥接模式（默认）		host是主机模式  none不配置网络
~~~

###### springboot项目打包成Docker镜像

~~~
1、编写成功Springboot项目

2、达成jar包

3、编写dockerFile进行发布

4、构建镜像

5、发布运行
~~~



