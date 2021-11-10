---
title: Linux常用命令
date: 2021-10-30 23:17:37
tags: ["Linux"]
cover: 
---

### Linux常用命令

##### 跳转目录

cd  跳转

##### 列出目录下的所有文件夹及文件

ls

##### 当前路径

pwd

##### 编辑文件

vim 文件名(如果没有这个文件会立即创建)

然后按i ，进入编辑模式（才能改文件）

编辑完，按ESC，再按:wq（保存退出）

##### 查看防火墙状态

systemctl status firewalld

##### 查看防火墙开启端口

firewall-cmd --list-all

##### 开放指定端口

firewall-cmd --add-port=端口号/tcp --permanent

--add-port=端口号/tcp  添加端口 /协议

--permanent 表示永久生效（不然下次关机后开机又没了）

**注意开启端口后必须重新加载防火墙**

##### 重新加载防火墙

firewall-cmd --reload

##### 解压文件到指定目录

tar -zxvf 解压文件名 -C 指定的目录

##### 压缩文件

tar -zcvf 压缩后的文件名 压缩的目录

##### 查看相关进程

ps -ef | grep 相关进程

列如：

ps -ef | grep tomcat

##### 强制杀死某个进程

kill -9 进程号
