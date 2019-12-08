---
title: jenkins前端自动部署入门
date: 2019-11-36 23:10:17
categories: js
top: 100
tags:
    - jenkins
    - 部署
photos: 
    - "https://desk-fd.zol-img.com.cn/t_s1024x768c5/g5/M00/0C/02/ChMkJ13V9q6IOSp_AAPNPlvQOwwAAvV_QJYgfoAA81W947.jpg"
---
<!--more-->

## jenkins前端自动部署入门

由于前端项目打包后是静态资源，不需要进程守护。一般地，前端项目使用jenkins来进行自动部署，包括打包、测试等一系列流程

由于 jenkins是基于java环境运行的，所以首先需要安装java环境

参考： 
[https://jingyan.baidu.com/article/375c8e190c1f9525f2a22931.html](https://jingyan.baidu.com/article/375c8e190c1f9525f2a22931.html)

#### 下载
1、下载地址：https://jenkins.io/download，

下载最后war包, 下载完成后进入下载后的目录，在`cmd`中输入 `java -jar jenkins.war ` 然后在页面输入`localhost:8080`
#### 初始化

　　启动jenkins服务后，可以在8080端口访问到jenkins
![](https://pic.xiaohuochai.site/blog/jenkins1.png)

然后在服务器的指定目录找到密码登录

/var/lib/jenkins/secrets/initialAdminPassword

按照默认配置安装插件 

![](https://pic.xiaohuochai.site/blog/jenkins2.png)

等待插件安装完成

![](https://pic.xiaohuochai.site/blog/jenkins3.png)

创建一个管理员账户，完成配置后，就可以登录 Jenkins 了

![](https://pic.xiaohuochai.site/blog/jenkins4.png)

#### 安装插件
下面来安装nodejs插件
![](https://pic.xiaohuochai.site/blog/jenkins6.png)

可以看到，Jenkins提供了丰富的插件供开发者使用，找到需要的[NodeJS Plugin]，勾选后点击安装即可

![](https://pic.xiaohuochai.site/blog/jenkins7.png)

> 可能会出现网络不好或者被墙的情况，可以换一下域（百度一下）

#### 配置任务
1. 安装好github钩子以及nodejs插件后，接下来开始配置任务

　　点击创建一个新任务，填写任务名称，构建的项目类型可根据实际情况进行选择，本次选择第一种即可
　　
![](https://pic.xiaohuochai.site/blog/jenkins5.png)

2. 配置基础信息

![](https://pic.xiaohuochai.site/blog/jenkins12.png)

3. 往下拉，看到源码管理，点选Git，依然填写博客对应的Repo地址
![](https://pic.xiaohuochai.site/blog/jenkins13.png)

4. 继续往下拉，在构建触发器单击增加构建步骤，在弹出的下拉菜单中选择`Execute shell。勾选GitHub hook trigger for GITScm polling`。构建环境选择nodejs

![](https://pic.xiaohuochai.site/blog/jenkins14.png)
5. 构建过程
一般地，构建过程，输入如下 
```
npm install &&
npm run build
```

### 问题处理：

1. 假如证书有问题的话会报错：
![](https://img2018.cnblogs.com/blog/1406304/201905/1406304-20190527105223189-1051377169.png)
 可以点击：  https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

 滑动到最后，配置修改地址，将https改成http，

http://updates.jenkins.io/update-center.json

或者

https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

然后点击submit->checknow

2. 插件管理页面提示：There were errors `checking the update sites:IOException:Unable to tunnel through proxy.Proxy returins "HTTP/1.1 400"`
![](https://img2018.cnblogs.com/blog/1406304/201905/1406304-20190524095823763-636781851.png)
解决办法和第一个是一样的。

参考文档：
- [Jenkins+GitHub集成接口测试环境部署](https://blog.csdn.net/lykio_881210/article/details/88557728)
- [jenkins实战（一）：war安装及插件安装](https://www.cnblogs.com/grey-wolf/p/8716662.html)
- [jenkins简单安装及配置（Windows环境）](https://www.cnblogs.com/imyalost/p/8677345.html)
- [Jenkins安装与插件安装连接超时问题](https://blog.csdn.net/GISuuser/article/details/100121237)
- [jenkins+github集成自动化测试环境搭建（1）-jenkins和github安装](https://blog.csdn.net/u011922006/article/details/88187060)
