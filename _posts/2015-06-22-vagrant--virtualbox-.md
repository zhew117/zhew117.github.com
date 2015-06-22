---
layout: post
title: "vagrant + VirtualBox 工作环境配置"
description: ""
category: 
tags: []
---
{% include JB/setup %}


##WHY VAGRANT?

Vagrant provides easy to configure, reproducible, and portable work environments built on top of industry-standard technology and controlled by a single consistent workflow to help maximize the productivity and flexibility of you and your team.

vagrant提供建立在行业标准技术顶部和控制的一个一致的工作流程，以帮助最大限度地提高您和您的团队的工作效率和灵活性，易于配置，重现性好，轻便的工作环境。

简单来说就是在同一个虚拟机环境下进行开发，不再需要配置本地系统环境，避免了“我的机器上是好的，为什么服务器上出问题了”这种问题的发生，而且有些软件在Windows下无法部署。

首先拿到虚拟机文件，

>>vagrant init

然后

>>vagrant box add
 
然后

>> vgarant up

虚拟机就启动起来了

查看虚拟机状态

>> vagrant status 

链接到虚拟机

>>vagrant ssh

或者

>>ssh vagrant@localhost -p 2222

链接上了之后就rails s，grunt launch什么的把服务器开起来就可以在“本地”跑了，

项目文件夹作为共享文件夹。

刚刚转到前端开发，下一篇写一下整个前端的架构。




