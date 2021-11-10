---
title: 记录第一次使用linux部署springbootweb项目
date: 2021-10-21 14:49:29
tags: ["linux","项目部署"]
cover: https://ns-strategy.cdn.bcebos.com/ns-strategy/upload/fc_big_pic/part-00595-3869.jpg
---

### 记录第一次使用linux部署springbootweb项目

#### 收获感受

经过一下午的安装虚拟机、Linux系统、JDK1.8，项目打包，解决bug，终于项目部署成功！哈哈哈哈哈哈哈哈哈

收获还是不错的，期间安装了xShell、Xftp（负责文件传输），解决防火墙端口开放，springboot项目的打包问题。

先来看看效果图吧！

![部署成功](https://img-blog.csdnimg.cn/3514cd9933034f578dca24075a705aba.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

还记得昨天晚上通过一系列的bug解决，效果出来的时候，左手拳一挥，大喊：“nice，搞出来了”的兴奋（也许这就是解决代码的快乐）

那来回顾一下过程是怎样的吧！

首先安装虚拟机和centos7就不用说了吧！（网上大部分的教程，跟着做就可以了！）

其次虚拟机的网络模式必须选择桥接模式，为什么啊？还有什么模式啊？

有桥接模式、NAT模式、仅主机模式

- 仅主机模式：仅主机模式不说了，最垃圾
- NAT模式：代表在电脑中模拟一个路由，由这个路由给虚拟机分配ip，也就是说虚拟机的网络是当前电脑的子网络（虚拟机能访问当前电脑，但是当前电脑不能访问虚拟机）这种模式相对安全，但是不符合我们部署测试，因为我们项目部署在虚拟机linux中，我们要用当前电脑测试
- 桥接模式：虚拟机网络网段与当前电脑网段是同一个网段，这种方式会相对不安全。但是我这小电脑也没什么可黑的。

#### 相关步骤

安装好这些之后，再安装xShell、Xftp（负责文件传输），通常服务器也是用这两个进行交互，下面是xShell5、Xftp5版本

链接：https://pan.baidu.com/s/13RGxoATGUkZMfzwYM85Tlg 
提取码：h9hp

下载好之后，解压，进入Xshell+Xftp文件夹，点击!绿化.bat，之后就可以用了。

打开虚拟机linux系统，打开终端，输入ifconfig，查看当前ip地址

打开Xshell，输入linux系统IP地址，连接上linux系统。

Xshell中点击文件传输，自动打开Xftp，负责文件传输。

##### 安装JDK

下载好JDK1.8的linux系统的解压包，利用Xftp将压缩包传输到linux。

举例：

```xml
tar -zxvf /export/sort/jdk-8u144-linux-x64.tar.gz -C /export/install/
```



```
tar -zxvf 压缩包位置 -C 解压之后文件放的位置
```

-C：表示指定文件解压位置

##### 配置环境变量

编辑配置文件（vim是打开编辑页面）

```
vim /etc/profile.d/java.sh
```

按i编辑

```
export JAVA_HOME=/export/install/jdk1.8.0_144 
export PATH=$PATH:$JAVA_HOME/bin
```

将上方JAVA_HOME改成jdk安装目录

按esc，退出编辑模式，在输入：wq，表示保存退出

输入jps，如果出现数字表示配置成功

输入java -version，出现java版本表示成功

#### 打包springboot项目jar

由于集成了Maven，所以打包部署简单，

我这里之前写了一个springsecurity的小demo

maven打包springboot项目必须在pom文件中加上(不加会出错)

```xml
<!--加了这个才能在linux部署-->
<plugin>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
<!--加了这个才能在linux部署-->
```

然后点击package

![在这里插入图片描述](https://img-blog.csdnimg.cn/5698fc89bb714c18aa034e5dfba26aad.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

idea会在target目录下生成jar包，利用Xftp把jar包复制到linux中

#### 运行项目

Xshell命令行转到jar包所在目录

输入

```
java -jar jar包名
```

项目成功开始运行，但是我试了在window上不能正常访问，于是我又在linux中访问，发现访问成功

于是我开始怀疑防火墙问题。



查看防火墙状态

```
systemctl status firewalld
```

如果显示active状态，表示防火墙已开启，如果没开启

打开防火墙命令

```
systemctl start firewalld
```

查看所有永久开放的端口

```
firewall-cmd --list-ports --permanent
```

如果没有web项目所占用的端口，说明防火墙拦截了

所以得开放防火墙端口

```
firewall-cmd --add-port=8080/tcp --permanent
```

配置结束后需要输入重载命令并重启防火墙以生效

```
firewall-cmd --reload

systemctl restart firewalld
```

然后访问就可以了
