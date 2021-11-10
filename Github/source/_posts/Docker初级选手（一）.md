---
title: Docker初级选手（一）
date: 2021-10-31 11:26:44
tags: ["docker"]
cover: https://img1.baidu.com/it/u=1589818293,2913011842&fm=26&fmt=auto
---

### Docker初级选手（一）

##### 卸载

1、卸载docker包

yum remove docker-ce

2、因为主机上的图像、容器、卷、自定义配置不会自动删除，所以需要手动删除相关文件

rm -rf /var/lib/docker

##### Centos7安装

1.安装docker所需依赖

```shell
yum install -y yum-utils
```

2.设置yum安装时所用的镜像源

*# 阿里仓库* 

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

3.安装docker

```shell
yum install docker-ce docker-ce-cli containerd.io
```

4.启动并设置开机自启docker

```shell
systemctl start docker 

systemctl enable docker
```

5.查看docker是否安装成功

```shell
docker version
```

##### 解决docker pull慢的问题

将docker镜像源修改为国内的：

在 /etc/docker/daemon.json 文件中添加以下参数（没有该文件则新建）：

```
{
  "registry-mirrors": ["https://9cpn8tt6.mirror.aliyuncs.com"]
}
```

服务重启：

```
systemctl daemon-reload
systemctl restart docker
```

