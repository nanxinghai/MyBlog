---
title: Docker镜像、容器常用命令（三）
date: 2021-10-31 18:41:56
tags: ["Docker"]
cover: https://s3.bmp.ovh/imgs/2021/09/6e2de1017d5be764.png
---

### Docker镜像、容器常用命令（三）

##### 1.基础命令

```shell
docker version          #查看docker的版本信息 
docker info             #查看docker的系统信息,包括镜像和容器的数量 
docker 命令 --help       #帮助命令(可查看可选的参数)
```

##### 2.镜像命令

###### 1.查看所有镜像

```shell
docker images 
```

结果是一张二维表，跟数据库一样

```
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    feb5d9fea6a5   5 weeks ago   13.3kB
```

REPOSITORY ：表示镜像名

TAG： 表示版本 （latest表示最新版）

IMAGE ID ：表示镜像ID

CREATED： 表示该镜像是官方是什么时候更新的

SIZE: 表示该镜像的大小

```shell
docker images -q   #只显示镜像的ID
```

###### 2.搜索镜像

```shell
docker search 镜像名  #如果要搜索版本（默认最新版），那么在后加：版本号
#列如    docker search mysql:5.7
```

###### 3.下载镜像

```shell
docker pull 镜像名 #如果要下载版本（默认最新版），那么在后加：版本号
#列如    docker search mysql:5.7
```

###### 4.删除镜像

```shell
docker rmi -f 镜像名/镜像ID  # 删除镜像，后面可以是镜像名，也可以是镜像ID
```

递归全部删除（删库跑路）

```shell
docker rmi -f $(docker images -q)
```

##### 容器命令

容器就是跑起来的镜像（必须要有镜像）

###### 1.新建容器并启动

```shell
docker run [参数说明] image

参数：
	-name # 表示当容器启动起来时，给他取一个名字（因为后面有集群,用于区分）
	-d	  # 以后台方式运行
	-it   # 以交互模式运行，一般在最后跟上/bin/bash
	-p    # 指定外部映射端口  例如8080:80  外部访问8080实际是访问80
	-P(大写) # 随机指定端口
```

###### 2.在交互模式退出容器

```shell
exit #退出并结束容器
Ctrl+P+Q #退出但不结束容器（就是在后台运行）
```

###### 3.查看正在运行中的容器

```shell
docker ps
```

###### 4.查看所有的容器

```shell
docker ps -a
```

###### 5.删除容器

```
docker rm -f 容器ID  
```

###### 6.进入正在执行的容器

```shell
docker exec -it 容器ID /bin/bash #进入容器后开启一个新的终端
docker attach 容器ID 	#进入容器后就是原来的终端
```

###### 7.容器内文件拷贝到主机上

```shell
docker cp 容器ID:容器内的路径 主机上的路径 
```

**注意：执行语句时，必须在主机地址上**

###### 8.挂载数据卷

例如：我们在docker中运行mysql，但是mysql的数据都存在容器中，如果我们把容器删了，那数据不是就丢失了吗？

所以在容器启动时，可以将docker容器中的目录与我们liunx中的目录对应起来，这样数据就不会丢失了。

就是在run时添加-v参数

```shell
docker run [参数说明] image

参数：
	-v 本地目录:容器目录
```

###### 9.查看容器详情信息(元信息)

```shell
docker inspect 容器名/容器ID
```

