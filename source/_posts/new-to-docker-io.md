title: Docker初探
date: 2014-08-21 16:46:03
tags: 
	- docker
	- devops
	- ci

categories:
    - programming
    - tools
---

![](http://www.cstor.cn/webedit/uploadfile/20140626151501579.jpg)


## 工作步骤

Docker所用的虚拟镜像的基线版本都是Ubuntu系统。
当运行`docker run xxx`之后，
1. Docker客户端连接到Docker守护进程
2. 如果本地没找到xxx镜像的话，Docker守护进程就从Docker Hub仓库中拉下(pull) xxx镜像
3. Docker守护进程根据xxx镜像创建一个新的容器(container), 从其中来执行程序并产生输出内容
4. Docker守护进程将输出内容流到(stream)Docker客户端，然后发送到终端


## 常用命令
### 官网入门教程的几个常用命令
	docker version
	docker search tutorial
	docker pull learn/tutorial
	docker run learn/tutorial echo "hello world"
	docker run learn/tutorial apt-get install -y ping
	docker ps -l
	docker commit 698 learn/ping
	docker run learn/ping ping google.com
	docker ps
	docker inspect efe
	docker images
	docker push learn/ping
<!--more-->


### 删除镜像和容器
	docker rmi -f learn/ping
	docker rm -f learn/ping
	
### 容器之间通信
	docker run -d --name web1 larrycai/ubuntu-sshd
	docker run -it --link web1:web1_1 dockerfile/ansible

* 通过`run`命令的`--name`参数可以建立容器的别名，便于docker去管理。
* 通过`run`命令的`-it`参数可以进行交互式的会话。
* 通过`run`命令的`--link`参数可以建立一个容器到另一个容器的链接，使之互相访问。每个容器都会分配一个私网地址 `172.17.0.xx`，容器之间可以进行用tcp/ssh/http通过私网IP或者容器名来进行访问。


### 映射容器的端口到宿主机


我们以[docker仓库中的Mysql镜像](https://registry.hub.docker.com/_/mysql/)为例，在docker中启动一个包含mysql-server的容器。

	docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=mysecretpassword -d  -p 49111:3306 mysql

* 通过`run`命令的`-p`参数可以将容器的端口映射到宿主机，这样宿主机也能访问容器内的服务了（不指定IP的话，是映射到0.0.0.0，即docker环境虚拟机的地址，非127.0.0.1）。
* 通过`run`命令的`-e`参数可以向容器内设置环境变量，这样宿主机就可以在启动时插入一些参数到容器。注意插入容器的参数会根据镜像名加上前缀，比如`MYSQL_ROOT_PASSWORD`在容器内会表示为`MYSQL_ENV_MYSQL_ROOT_PASSWORD`
	
	
可以在其他容器中来访问mysql-server服务：

	docker run --name some-app --link some-mysql:mysql -d application-that-uses-mysql

比如可以进入该容器的命令行来用mysql进行连接：

	docker run -it --link some-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'

最后要说的就是，还可以从docker环境（宿主机）中来访问该mysql-server服务！在你的boot2docker环境下，可以找到这样一个环境变量`DOCKER_HOST="tcp://192.168.59.103:2375"`，因为boot2docker本身其实就是启动了一个虚拟机，并在其中安装了docker命令行，因此这个地址就是该虚拟机的地址。随后通过虚拟镜像启动的容器Container都是运行在这个虚拟机之中的。
本例中，笔者要从自己的Mac上访问到刚刚启动的mysql-server容器，可以用mysql客户端连接到`mysql://root:mysecretpassword@192.168.59.103:49111`


![](http://res.uperform.cn//Docker%20Structure.png)

## 坑！


### Docker 0.9.1目前只支持基于64位系统！
在Linux系统，特别是Ubutnu上支持得最好。MacOS下有个一键安装包。

用`sudo service docker.io  status`命令查看，发现docker的deamon一直没能启动。
表现为任何docker命令都返回`dial unix /var/run/docker.sock: no such file or directory`
原来，docker目前只支持Ubuntu **64-bit** ! 早说啊。

### Mac下的boot2docker在共享文件时有bug！
在Mac下用boot2docker，借助`docker run -v pathOfHost:pathOfContainer`命令进行文件共享，会有很多文件无法成功共享的缺陷。据说在Windows下的boot2docker就没问题。

准备尝试一下etherpad工具。

## 在线资源
* [Docker —— 从入门到实践- GitBook](http://yeasy.gitbooks.io/)
* [Fig | Fast, isolated development environments using Docker](http://www.fig.sh/)
* https://github.com/chaifeng/docker-etherpad
* ustack.com
* https://codio.com/
