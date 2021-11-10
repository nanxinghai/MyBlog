---
title: Docker制作镜像（四）
date: 2021-10-31 21:43:48
tags: ["Docker"]
cover: https://z3.ax1x.com/2021/09/25/4r8KHJ.jpg
---

### Docker制作镜像（四）

众所周知，docker的镜像是有分层机制的，比如当你下载一个镜像时，你常常会看到以下画面

![在这里插入图片描述](https://img-blog.csdnimg.cn/cb1789e8e3134cc1bb447ce97ac9d04c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

你发现pull complete了许多，这些全是文件的分层。

这样有什么优势呐？

文件出现分层了之后，如果不同的环境有相同的文件分层就可以不用下载了，对吧

容器和镜像的区别就是，容器就是在镜像的最上层加了一层读写层，所有对容器的修改都在最上层，不会对镜像产生影响。

那么我们制作镜像就可以利用容器来制作。

#### 制作镜像的方式

1.基于容器创建

2.基于Dockerfile创建

3.maven插件构建（原理也是基于Dockerfile）

##### 一、基于容器

```shell
docker commit [参数] 容器ID 镜像名:版本号
```

```
参数说明：
-a="作者"
-m="提交信息"
```

##### 二、使用Dockerfile

编写Dockerfile文件就能制作镜像 。就是用命令行制作镜像。

1.编写Dockerfile文件

2.docker build 制作镜像

3.docker run 运行镜像成一个容器

4.docker push 发布镜像（Docker Hub、阿里云镜像仓库）

![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg2020.cnblogs.com%2Fblog%2F1869289%2F202005%2F1869289-20200529090758309-649751292.png&refer=http%3A%2F%2Fimg2020.cnblogs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1638453884&t=bb2602fb624184927c7f4d58d0d3206d)

dockerfile有许多命令行。

**基础知识**

1.所有关键字必须大写

2.执行顺序从上到下

3.每一行命令都会生成一个镜像层

4.#表示注释

```shell
FROM				#指定基础镜像
MAINTAINER			#镜像的作者 姓名+邮箱
RUN					#镜像构建的时候需要运行的命令
ADD					#添加命令 添加 tomcat镜像，
WORKDIR				#镜像的工作目录
VOLUME				#挂载的目录
EXPOSE				#对外暴露的端口 -p一样
CMD					#指定这个容器启动时要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT			#指定这个容器启动时要运行的命令，可以追加命令
COPY				#类似ADD命令，将我们的文件拷贝到镜像中
ENV					#构建的时候设置环境变量
```

**实战测试**

构建一个相对完整的centos镜像，编写dockerfile镜像

mydockerfile：

```shell
FROM centos
MAINTAINER fanqie<2045698851@qq.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80
CMD echo $MYPATH
CMD echo "-----end----"
CMD /bin/bash
```

运行docker build，执行镜像

docker build -f dockefile的地址 -t 生成的镜像名：版本号  . 

```sehll
docker build -f mydockerfile -t mycentos:1.0 .
```

##### 三、基于maven插件制作镜像

修改docker配置文件，允许docker远程访问，开启2375端口

1.编辑docker文件  /usr/lib/systemd/system/docker.service

```shell
vim /usr/lib/systemd/system/docker.service
```

2.修改ExecStart行，增加内容 -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock ，修改后如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/13227a490e3b4b00a5ea0b0284d489c8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

3.重新加载docker

```shell
systemctl daemon-reload
systemctl restart docker 
```

4.配置完成后 浏览器访问 http://192.168.30.108:2375/version

ip地址要换成你自己的ip地址，如果看到docker的版本信息，就说明配置成功

5.在工程pom文件中加入

```xml
<!--docker的maven插件，官网： https://github.com/spotify/docker‐maven‐plugin-->
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>0.4.13</version>
    <configuration>
        <!--镜像名 PetHome:1.0-->
        <imageName>${project.artifactId}:${project.version}</imageName>
        <!--基础镜像-->
        <baseImage>java:8</baseImage>
        <entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]</entryPoint>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}
                </directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
        <!--docker远程主机-->
        <dockerHost>http://192.168.1.40:2375</dockerHost>
    </configuration>
</plugin>
```

6.在其项目目录下输入打包命令

```shell
mvn clean package -Dmaven.test.skip=true docker:build
```

7.在linux中输入docker images 发现镜像成功打包，nice！

