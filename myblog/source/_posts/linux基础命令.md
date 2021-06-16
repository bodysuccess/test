---
title: linux基础命令
tags: linux
categories: linux
abbrlink: 6463939c
date: 2021-04-18 20:03:20
---

~~~shell
# 查看版本信息
uname -r
# 查看系统版本
cat /etc/os-release
#centos7的查看关闭开启防火墙命令
firewall-cmd --state					#查看防火墙状态
systemctl stop firewalld.service		#停止防火墙
systemctl disable firewalld.service 	#开机自启
~~~

###### 文件处理命令

~~~shell
# 显示目录文件
ls 
# 显示带隐藏的目录文件
ls -a
# 创建目录
mkdir 目录名
# 创建文件
touch 文件名
# 切换目录
cd  /目录
# 返回上一级目录
cd ..
# 显示当前目录
pwd
# 删除空目录
rmdir /目录
# 复制目录或者文件
cp -rp /源文件或者目录 /目标目录
# 剪切文件或目录
mv /源文件或者目录 /目标目录
# 删除文件或目录
rm -rf /目录或文件
# 查询文件信息
cat 文件名  
tail -f 文件名  #实时查询信息
tail -n 1000 文件名 #倒叙查询1000行信息
~~~

###### 权限管理命令

~~~shell
chmod 参数 文件名	#更改文件的读写执行权限  参数有-r -w -x 读 写 执行 u所有人 g所属组 例如：chown U+x 123.log
chown 用户 文件名	#更改某个文件用户权限		用户必须是真实存在的
~~~

###### 文件查询命令

~~~SHELL
find /目录或文件 -name *init*	#查询含有init的文件 *可以匹配任意个字符
find /目录或文件 -name *init？？？	#一个？只能匹配一个字符
~~~

###### 用户管理命令

~~~shell
whoami 	#查看当前用户
useradd -m 账号	#新建一个账号
passwd 账号		#给此账号输入密码
userdel -r 账号 	#删除账号信息
~~~

###### 其他命令

~~~SHELL
cal -1 5 2020  #查看2020年5月份的日历
wc -lc 文件名 >要创建的文件			# 统计某个文件的行数和列数要书写到某个文件
wget 远程地址			#下载某个文件从远程地址中
~~~

