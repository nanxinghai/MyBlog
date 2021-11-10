---
title: 入职必备两小时入门SVN（二）
date: 2021-11-09 19:15:30
tags: ['SVN']
cover: https://img-blog.csdnimg.cn/4603fd86f1e24017b069efc87433e7c3.png
---

### 入职必备两小时入门SVN（二）

#### 1.SVN使用

###### 1.创建版本库

打开SVN服务端（开始菜单）

右击Repository、点击Create Repository，出现下面画面，选择默认就好

![在这里插入图片描述](https://img-blog.csdnimg.cn/918258d101b147ef9c8aed5d9b9fb80d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_19,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/662e8dc7510447fbb5480ceae737a410.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_19,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/e79b456895c54da5846860fa8978abbb.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_19,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/855a71e7431a4d079a9ec8a80edd327b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_19,color_FFFFFF,t_70,g_se,x_16)

###### 2.导入项目到SVN

![在这里插入图片描述](https://img-blog.csdnimg.cn/a0ca6c4d44bc497889d3c628356a3cd5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

复制仓库的url地址

找到你要上传的文件夹（或者文件），右键，选择import

![在这里插入图片描述](https://img-blog.csdnimg.cn/533ec7b1595c4a53be98e77003ee9e7a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/8e44ca7032b841d0b2b9ba9dc4ebbb6d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_18,color_FFFFFF,t_70,g_se,x_16)

url：复制刚刚的仓库地址，**一般会再加上你自己的文件夹名字**

message：你随便写，一般写你完成了什么业务

点击ok，会让你授权，一般永久授权就可以了。

会让你输入用户名密码，就输入之前的username和password就行了

![在这里插入图片描述](https://img-blog.csdnimg.cn/b4855ab578b449f7a4bb7612d46e363d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

看到版本已经变成2，那么已经成功！

也可以去SVN服务端查看，或者进入浏览器访问

###### 3.检索项目（拉代码）Checkout

别人会给你一个svn地址。拿着地址在你想拉代码的文件夹右击鼠标，有个Checkout

![在这里插入图片描述](https://img-blog.csdnimg.cn/e5bbf1d6f35a46419f645c70f40054cc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/96678e52b36e4d25a7f72ee5f895f82b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_18,color_FFFFFF,t_70,g_se,x_16)

###### 4.提交项目

当你修改了项目之后，要把项目提交到版本库中。**一般新加的文件，没有添加到版本控制中，所以需要先单独add以下。**

或者可以直接提交整个项目，在提交页面选择未被add的文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/2f03a20ccec8435aa316c499ea8d7b05.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/43e5d08e48c1492099682e070dfa9040.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

###### 5.更新项目

如果版本库中的代码与你的客户端不一致时，直接右击update就可以了

![在这里插入图片描述](https://img-blog.csdnimg.cn/aec39eeaab9441b4988a5daaeac64e12.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

###### 6.解决冲突

版本冲突原因：

​		**假设A、B两个用户，他们分别从svn服务器中检出了test1.txt文件，此时A、B、服务器三个地方的test1.txt的版本都是13(我测试环境的当前svn赋予的版本号)接下来，B用户添加一句话并提交      此时B用户和服务器的test1.txt的版本都变为14，只有A用户的test1.txt的版本还为13。接下来A用户添加一句“aa”,然后提交   由于A用户是在13版本上做的修改，而服务器已经是14版本了，所以会提交失败**

**一般都是edit解决冲突。**



一般会在提交前先update一下，如果发现冲突。

![在这里插入图片描述](https://img-blog.csdnimg.cn/28b9174cc75a4a9ba5920844a9fcea8a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)





![在这里插入图片描述](https://img-blog.csdnimg.cn/0c4ff21ab4c641f19997e6bb6abe1a4b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

你会发现多了两个文件，一个是上个版本的，一个是现在这个版本的，还有一个是自己本地的嘛

解决办法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/45bb88deb7b149ff9eec4d9c64bcd7dc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/9411d14fb75c49698fc4f5d4bbf85300.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

一般就是把你的版本与服务器的版本结合起来，cv到merged中

#### 2.IDEA中集成SVN

配置SVN环境

File》Other Settings》Version Controller》Subversion

![在这里插入图片描述](https://img-blog.csdnimg.cn/9cebfcff386948a29982c75040b342f1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

重启IDEA

###### 1.检出项目

![在这里插入图片描述](https://img-blog.csdnimg.cn/03b9db7f38b4449eacf4b8d75d27d8be.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/213d25528ce149fbb666ad1770059583.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

选好仓库地址之后，选中checkout

![在这里插入图片描述](https://img-blog.csdnimg.cn/cc61c573d21e4bb994845c106e4d8f57.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_16,color_FFFFFF,t_70,g_se,x_16)

选择存放的目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/61209123457a4f339f4cb70dacc8915b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_18,color_FFFFFF,t_70,g_se,x_16)

选择默认的1.8

![在这里插入图片描述](https://img-blog.csdnimg.cn/71b3800cda504317889d82eca3ceb20e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_14,color_FFFFFF,t_70,g_se,x_16)

之后就可以打开项目了。

###### 2.提交项目

![在这里插入图片描述](https://img-blog.csdnimg.cn/9261b43a8234460aab3f337ace59a502.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d71127f0f125457796999fe4004b5fe5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

点击commit即可

###### 3.更新代码

![在这里插入图片描述](https://img-blog.csdnimg.cn/afcb6f1e56c946d59a5490d02a201285.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

选择默认就可以了

![在这里插入图片描述](https://img-blog.csdnimg.cn/d3386b587c1043dc928bdd572f9d8c6b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_17,color_FFFFFF,t_70,g_se,x_16)

###### 4.解决冲突

![在这里插入图片描述](https://img-blog.csdnimg.cn/fa3ca17e5fb8412a95813fae837aff92.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2f86ee26496d44d2a34b961cfc2274db.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA55Wq6IyE5aW95Zuw5ZWK,size_20,color_FFFFFF,t_70,g_se,x_16)

