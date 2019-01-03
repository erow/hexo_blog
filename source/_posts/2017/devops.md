---
title: "devops"
date: "2019-01-02 13:01"
categories：
    -运维
tags:
    -ci
---

# 自动化部署

让机械的流程成为真正的程序。

这里我用的是daocloud。

基本步骤如下：

![](/blog_images/流程.png)关于测试这一块不太懂。既然应用都没有构建，测试什么呢？

在build阶段，根据Dockerfile生成应用的image。

![](/blog_images构建任务.png)构建参数对应dockerfile的内容。在操作平台右侧有环境变量。但是好像不能在构建的时候传入，如何导入参数这一块还有待了解。

在运行环境中的参数在应用设置里。

![](/blog_images应用设置.png)云隧道功能可以在内网环境中部署公网应用。



# Travis CI Tutorial

但是对于某些应用使用daocloud还是太笨重了。因为docker还包含了应用运行的环境。



