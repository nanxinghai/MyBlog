---
title: 入职必备两小时入门SVN（一）
date: 2021-11-09 17:13:15
tags: ["SVN"]
cover: https://img-blog.csdnimg.cn/4603fd86f1e24017b069efc87433e7c3.png
---

### 入职必备两小时入门SVN（一）

#### 1.简介

SVN的全称是Subversion，即版本控制系统。它是最流行的一个[开放源代码](https://baike.baidu.com/item/开放源代码/114160)的版本控制系统。作为一个开源的版本控制系统，Subversion管理着随时间改变的数据。这些数据放置在一个中央资料档案库（Repository）中。这个档案库很像一个普通的[文件服务器](https://baike.baidu.com/item/文件服务器/10037219)，不过它会记住每一次文件的变动。这样就可以把档案恢复到旧的版本，或是浏览文件的变动历史。Subversion是一个通用的系统，可用来管理任何类型的文件，其中包括程序源码。

#### 2.主要作用

1.目录版本控制

能够依时间来跟踪文件和目录。文件和目录都能进行控制。

2.版本历史

Subversion中，可以增加（add）、删除（del）、复制（cp）、和重命名（rename）、无论是文件还是目录，所有新加的文件都是从新版本开始

3.自动提交

一个提交，要么全部提交，要么全部不提交（事务）

#### 3.基本概念

- Repository(源代码库)：源代码统一存放的地方

- Checkout（提取）：当你的手上没有源代码的时候，你需要从Repository中Checkout一份

- Commit（提交）：当你已经修改了代码，你就需要Commit到Repository中

- Update（更新）：当你已经Checkout了一份源代码，Update后就可以和Repository上的源代码同步

#### 4.工作流程

![在这里插入图片描述](https://img-blog.csdnimg.cn/212dd2cec3a44482bc5c603081920424.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 5.生命周期

1.创建版本库

就是一个集中的空间，存放开发者所有的工作成果。他不仅仅能存放文件，还记录每次修改的历史，方便回退。一般在创建版本库时，会让你提供一些信息来标识这个版本库。

2.检出（Checkout）

Checkout（检出）操作是从版本库创建一个工作副本。工作副本是开发者的私人工作空间，可以进行内容的修改，然后提交到版本库中。

3.更新

update操作是用来更新工作副本的。由于版本库是团队合作的，所以你的工作副本可能会过期。假如：tom和jerry是一个公司的，他们 同时从版本库中checkout了一份工作副本，此时，工作副本与版本库是同步的。由于tom的效率非常快，一下子工作就做完了，并且commit了代码。此时，jerry的工作副本就过期了，需要update才能与版本库一致。

4.执行变更

当你检出之后，你可以做很多操作。你可以编辑已经存在的文件。

#### 6.安装配置

1.安装

​	SVN是包括服务端与客户端的，个人认为最好用VisualSVN server 服务端和 TortoiseSVN客户端搭配使用.

下载地址：http://subversion.apache.org/packages.html

​	两个下载好之后默认配置，无脑下一步就可以了，**注意：在安装TortoiseSVN（小乌龟时），需要勾选command line client tools**

安装教程https://www.cnblogs.com/fandelong/p/4422977.html

2.配置

​		服务器需要配置ip、端口、账号、密码供客户端使用

![在这里插入图片描述](https://img-blog.csdnimg.cn/b1e0129e9a3b44bcbd9a92b2a332a902.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

然后选network

![在这里插入图片描述](https://img-blog.csdnimg.cn/03e190f595bc4d43925e39d2d8537fee.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

Server name的值可以设置为

1. ​    127.0.0.1（只能自己本机访问）
2. ​     当前ip地址（能ping通ip的都能访问）
3. ​     电脑用户名（只能自己本机访问）

SVN没有账号密码是不能进去的

配置账号密码

![在这里插入图片描述](https://img-blog.csdnimg.cn/0276ef86708b4d5bb22d30b1d33c5b0e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_11,color_FFFFFF,t_70,g_se,x_16)

右键User，create user

创建好之后，访问Repository

右键browse，浏览器访问，出现以下画面，已经配置成功（里面是空的，因为我们还没有创建版本库）

![在这里插入图片描述](https://img-blog.csdnimg.cn/eeede45867d64685a24813cba58713a0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

