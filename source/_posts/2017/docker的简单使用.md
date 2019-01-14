---
title: "docker的使用"
date: "2016-05-02 13:01"
categories:
    - 运维
tags:
    - docker
---
# 基本概念


1. 镜像 :是一个只读的模板，包含了**系统 **和** 运行程序**，相当于一份虚拟机的磁盘文件。

2. 容器 :当镜像启动后就转化为容器，即**运行状态**。在容器内的修改不会影响镜像，程序的写入操作都保存在容器中。

3. 仓库 : 集中存放镜像文件的场所 。


# 镜像的基本操作

1. ##本地镜像
    保存:`docker save -o file image` ，载入:`docker load file`

    从容器里获得镜像:`docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`
2. ##网络镜像
    类似于git使用`docker pull [OPTIONS] NAME[:TAG|@DIGEST]`和`docker push [OPTIONS] NAME[:TAG]`
    
3. ## Dockerfile :自动构建镜像
    用法:`docker build`

    Dockerfile 的写法

    >1. 使用#来注释
    >2. FROM :指令告诉 Docker 使用哪个镜像(下载镜像）
    >3. RUN  :下载镜像后执行的命令结果会写入到镜像 中，比如安装一个软件包。
    >4. ADD  :src destination,将本地文件写入镜像。
    >5. CMD  :  command param1 param2 , 提供了容器默认的执行命令。 Dockerfile 只允许使用一次 CMD 指令 
    >6. EXPOSE : port, 指定容器在运行时监听的端口。
    >7.  ENTRYPOINT:配置给容器一个可执行的命令，这意味着在每次使用镜像创建容器时一个特定的应用程序可以被设置为默认程序 
    >8.  WORKDIR :path, 指定RUN、CMD与ENTRYPOINT命令的工作目录。
    >9.  VOLUME :[path], 授权访问从容器内到主机上的目录 
    >10 . ENV <key> <value> ; USER <uid> 

# 容器的基本操作

1. ##run:`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]` 从镜像中创建容器
    * -d :后台运行
    * -i :保持容器活动
    * -t :进行交互
    * -p [主机:port:port]:端口映射
    * -P :随机端口映射
    * -v:绑定外部存储空间 
    * 其他重要的:--name:容器命名，--link，--ip, --rm
2. ##start,stop :` docker start [OPTIONS] CONTAINER [CONTAINER...]`

    当使用run创建出新的容器后，使用这2个命令来控制容器的运行状态。

3. ##查看容器:`docker ps -a`
    
    不带参数则为当前运行状态的容器
4. ##进入容器

    1. ` docker attach [OPTIONS] CONTAINER` :把后台容器调到前端。

    2. `docker exec [OPTIONS] CONTAINER COMMAND [ARG...]`:在容器内新建一个命令。

    ``docker  exec -it  CONTAINER bash``

    3. 使用 nsenter 等软件。

5. ##连接容器

    `docker run --link  name:alias  CONTAINER `:将新建主机和被连接的主机组成一个局域网。

    name 是指被连接的容器，alias是在这个互联网络内name的主机名称。在新建的容器内使用alias进行访问（ping alias）

