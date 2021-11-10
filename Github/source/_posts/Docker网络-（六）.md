---
title: Docker网络(六)
date: 2021-11-03 22:57:11
tags: ["Docker网络"]
cover: https://z3.ax1x.com/2021/09/26/4cr9L4.jpg
---

### Docker网络(六)

#### 理解Docker0

先测试一手网络地址

![在这里插入图片描述](https://img-blog.csdnimg.cn/261050f2851f48a19db0f4e17a934fc3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

会发现一个Docker0网络地址

**这个Docker0就相当于一个路由器，每新建一个容器，就会为其分配一个ip，这个路由器会以桥接的模式连接我们的本机。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/e09a16145c6a4526b94f40fa883c5a98.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 容器和容器之间通信

容器和容器之间能不能直接通信？

**答案是不能的？容器和容器之间通过Docker0通信。而且也只能通过ip通信。**

**但是，这有一个缺点，就是ip是Docker0自动分配的，万一有一个容器挂了，现在重新启动，那ip不是就变了，原来要访问他的这个容器就访问不到了**

所以我们在容器启动时，可以给容器连接另一个容器（--link）

```shell
docker run -d --name 取名 --link 要链接的容器 启动的镜像
```

原理就是在etc/host文件中加ip 地址与容器名对应

这样就解决了这个问题。

但是又有新的问题来了。每一个容器我们都要去配这个link，太麻烦了。

#### 自定义网络

先看看docker网络

![在这里插入图片描述](https://img-blog.csdnimg.cn/1a6017c46da64be8adbf8092ff5b8721.png)

bridge：桥接模式



Docker0的网络就是桥接模式（但是不能通过域名访问）

我们要自定义实现网络，也是桥接模式

自定义网络

```shell
 docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
```

创建成功后，再次查看docker网络

![在这里插入图片描述](https://img-blog.csdnimg.cn/3338642f0c414f0d829f170bfd8f8e38.png)

等会启动容器的时候指定这个网络，就能不通过每次--link，直接实现域名访问

容器启动

```shell
docker run -d -p ... --name 容器名 --net 网络名 要启动的镜像
```

#### 网络连通

当两个容器处在不同的网络中时，他们又可以连接吗？答案肯定是不能的

查看docker网络

![在这里插入图片描述](https://img-blog.csdnimg.cn/78890aa2b5e94943be08f8d6464e9c64.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

我们发现可以将一个容器连接到一个网络中

实行这个命令看看

![在这里插入图片描述](https://img-blog.csdnimg.cn/b7ad765348924b6092a58c1366129af9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

```shell
docker network connect 网络名 容器名
```

就能将容器连接到别的网络中，相当于有两个ip地址

