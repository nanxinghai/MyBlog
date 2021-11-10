---
title: ElasticSearch的初级安装使用
date: 2021-09-25 08:23:12
tags: "ElasticSearch"
cover: https://z3.ax1x.com/2021/09/25/4r8KHJ.jpg
---

## **ElasticSearch简单安装使用**

------

### 基本概念

[ElasticSearch官网](https://www.elastic.co/cn/what-is/elasticsearch)

Elasticsearch 是一个分布式的开源搜索和分析引擎，适用于所有类型的数据，包括文本、数字、地理空间、结构化和非结构化数据

Elasticsearch 在 Apache Lucene 的基础上开发而成，支持Restful风格和多种语言调用

着重功能就是用来做**数据的检索和分析**

- **应用程序搜索**

- 网站搜索

- 企业搜索

- **日志处理和分析**

- 基础设施**指标和容器监测**

- 应用程序性能监测

- 地理空间**数据分析和可视化**

- 安全分析

- 业务分析

  

### 为什么要用ElasticSearch

可能会有人问，我们mysql也能做数据的检索和分析，为什么还要用ElasticSearch？

对，mysql也能做数据的检索和分析，但是我们说术业有专攻。而mysql主要用于做数据的持久化存储与管理，也就是crud。如果我们真的使用mysql做海量数据的检索与分析，ElasticSearch更在行。

#### 为什么说mysql做海量数据的检索与分析没有ElasticSearch在行

mysql做数据的检索与分析时，需要将全部数据都遍历一遍，所以速度会更慢。

而ElasticSearch在做数据的检索与分析时，并不需要遍历全部数据，所以速度会更快。

（你肯定又想问为什么ElasticSearch不需要遍历所有数据，后面的一篇博客会详情讲，先提示一下（倒排索引））

### ElasticSearch的安装（Window）

在安装ElasticSearch之前，必须安装JDK（最好安装JDK1.8版本及以上）

由于ElasticSearch 5.x 往后依赖于JDK 1.8的，所以现在我们下载JDK 1.8或者更高版本

我们会安装ElasticSearch、ElasticSearch-head插件（ElasticSearch可视化工具）、kibana(ElasticSearch可视化工具)

**最好是版本一致（避免版本冲突出错**）、**（我们这里是7.6.1版本）**

可以去官网找下载安装

也可以去阿里云盘下载、百度云盘

[阿里云盘下载地址](https://www.aliyundrive.com/s/4PoSeda3iLd)

[狂神说资料下载地址](https://pan.baidu.com/s/1PT3jLvCksOhq7kgAKzQm7g?from=init#list/path=%2F)

#### 解压三个压缩包即可

### 启动ElasticSearch及相关可视化工具

#### 1.找到ElasticSearch安装目录下的bin目录，双击elasticsearch.bat

![在这里插入图片描述](https://img-blog.csdnimg.cn/3d648c48c2004bf9b62cc83575553341.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

打开浏览器输入http://localhost:9200   看到以下画面则代表ElasticSearch已成功启动

![在这里插入图片描述](https://img-blog.csdnimg.cn/cc68f4288c2443b1ba837d5b5e8ab216.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 2.打开kibana安装目录下的bin目录，双击kibana.bat文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/4725f7a787824f41b35ae7420c018058.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

打开浏览器输入http://localhost:5601看到以下画面则代表ElasticSearch已成功启动

![在这里插入图片描述](https://img-blog.csdnimg.cn/09f1d2ff26074cf296ca6cc1ef1e75b2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 3.找到elasticsearch-head安装目录，执行npm install（nodejs项目安装依赖）

安装好之后再npm run start启动，,打开浏览器端口9100

**默认是打不开的（因为跨域）**

找到ElasticSearch安装目录下的config目录，打开elasticsearch.yml，在最后加上

```java
http.cors.enabled: true
http.cors.allow-origin: "*"
```

http://localhost:9100看到以下画面

![在这里插入图片描述](https://img-blog.csdnimg.cn/71a792ac36484db8a8bdaaf4e548f0e5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5ZGm5ZGm,size_20,color_FFFFFF,t_70,g_se,x_16)

至此，我们ElasticSearch、ElasticSearch-head、Kibana全部安装完成

下期介绍ElasticSearch的Restful风格做crud
