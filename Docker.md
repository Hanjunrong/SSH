Docker 命令速查手册

Docker 包括三个基本概念

镜像(Images)

仓库(Container)

容器(Repository)



启动命令

    service docker start 

^ 建立docker组: groupadd docker

^ 将当前用户加入docker组: usermod -aG docker $USER

启动容器

    docker run -it  ubuntu:14.04 bash

-it 交互式终端

--rm 容器退出后删除

bash 交互式shell

example : $ docker run --name webserver -d -p 80:80 nginx

查看容器内IP
```
docker inspect 容器ID
```
获取镜像

    docker pull [仓库名]

列出镜像

    docker images

自定义列表 docker images --format "table {{.ID}}\t{{.Reposity}}\t{{.Tag}}"

删除镜像

    docker rmi 
删除所有 docker rmi $(docker images -q)

```
docker	rmi	$(docker	images	-q	-f	dangling=true)
```

清除所有属于终止状态的容器  docker  rm  $(docker ps -a -q)

虚悬镜像

```
docker	rmi	$(docker	images	-q	-f	dangling=true)
```

查看镜像内的历史记录

```
docker history nginx:v2
```

慎用 docker commit

    docker commit --author "New Hello" --message "修改了默认网页" webserver nginx:v2
attach 命令

```
docker attach [容器名称]
```

当多个窗口同时attach到同一个容器,所有窗口同步显示

nsenter 命令

...

导出容器

```
docker export [容器ID] > [导出名称].tar
```

docker save 保存镜像到本地,含镜像历史

导入容器快照

```
cat ubuntu.tar | sudo docker import - test/ubuntu:v1.0
```

docker load也可以导入镜像存储文件到本地镜像库,含镜像历史

指定URL导入

```
docker import http://example.com/exampleimage.tgz example/imagerepo
```

查找镜像

```
docker search centos //查找官方镜像
```



Dockerfile 定制镜像

    $ touch Dockerfile

内容为:

    FROM nginx
    RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html

* FROM指定基础镜像   FROM scratch 从一个空白镜像开始

构建镜像

```
docker build -t nginx:v3 .
```

* RUN    1. shell格式  2. exec格式  RUN ["可执行文件", "参数1", "参数2"]

```
docker run --link redis-master:redis-master -v /usr/local/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave1 -d redis redis-server /usr/local/etc/redis/redis.conf
docker run --link redis-master:redis-master -v /usr/local/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave2 -d redis redis-server /usr/local/etc/redis/redis.conf
docker run --link redis-master:redis-master -v /usr/local/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave3 -d redis redis-server /usr/local/etc/redis/redis.conf
```

Dockerfile  指令

FROM   指定基础镜像

RUN      执行命令行命令

COPY    复制文件：

	格式:	
	COPY    <源路径>...	<目标路径>
	COPY    ["<源路径1>",...	"<目标路径>"]

​		原路径中可以使用通配符

```
COPY	hom*	/mydir/ 
COPY	hom?.txt	/mydir/
```

​		使用COPY指令，源文件的各种元数据都会保留

ADD    复制文件，仅在需要自动解压缩的场合使用 ADD。

比如官方镜像ubuntu中：

```
FROM	scratch 
ADD	ubuntu-xenial-core-cloudimg-amd64-root.tar.gz	/
...
```

CMD    容器启动命令

	CMD	指令的格式和  RUN	相似，也是两种格式：
	* shell		格式：	CMD	<命令>	
	* exec		格式：	CMD	["可执行文件",	"参数1",	"参数2"...]
	* 参数列表格式：	CMD	 ["参数1",	"参数2"...]	。在指定了ENTRYPOINT指令后，用   CMD指定具体的参数

ENTRYPOINT    入口点

```
FROM ubuntu:16.04
RUN apt-get update \
	&& apt-get install -y curl \
	&& rm -rf /var/lib/apt/lists/*
ENTRYPOINT [ "curl", "-s", "http://ip.cn" ]
```

存在ENTRYPOINT后,CMD的内容会作为参数传递给ENTRYPOINT

ENV    设置环境变量

```
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
```

ENV   NODE_VERSION    7.2.0

可以用$NODE_VERSION将环境变量取出

ARG     构建参数

```
格式： ARG <参数名>[=<默认值>]
```

设置构建环境的环境变量,容器运行时不存在这些环境变量

VOLUME   定义匿名卷

```
格式为：
	VOLUME ["<路径1>", "<路径2>"...]
	VOLUME <路径>
```

`VOLUME  /data`

挂载卷,事先指定某些目录挂载为匿名卷

运行时可以覆盖这个挂载设置    docker run -d -v mydata:/data  xxxx

EXPOSE   声明端口

```
EXPOSE <端口1> [<端口2>...]
```

运行时并不会因为这个声明开启这个端口的服务,作为镜像使用者方便配置映射.另作运行时随机端口默认映射

WORKDIR    指定工作目录

```
格式为 WORKDIR <工作目录路径>
```

使用 WORKDIR 指令可以来指定工作目录（或者称为当前目录），以后各层的当前
目录就被改为指定的目录，如该目录不存在， WORKDIR 会帮你建立目录。

USER    指定当前用户

```
格式： USER <用户名>
```

USER和WORKDIR都是改变环境状态并影响以后的层,USER改变之后层的执行RUN,CMD以及ENTRYPOINT这类命令的身份

这个用户必须是事先建立好的，否则无法切换。

```
RUN groupadd -r redis && useradd -r -g redis redis

USER redis

RUN [ "redis-server" ]

```

HEALTHCHECK    健康检查

```
格式：
	HEALTHCHECK [选项] CMD <命令> ：设置检查容器健康状况的命令
	HEALTHCHECK NONE ：如果基础镜像有健康检查指令，使用这行可以屏蔽掉
	其健康检查指令
```

告诉Docker应该如何进行判断容器的状态是否正常

初始状态:starting   检查成功后:healthy   连续一定次数失败:unhealthy

```
HEALTHCHECK 支持下列选项：
--interval=<间隔> ：两次健康检查的间隔，默认为 30 秒；
--timeout=<时长> ：健康检查命令运行超时时间，如果超过这个时间，本次健康检查就被视为失败，默认 30 秒；
--retries=<次数> ：当连续失败指定次数后，则将容器状态视为unhealthy ，默认 3 次。
```

和 CMD , ENTRYPOINT 一样， HEALTHCHECK 只可以出现一次，如果写了多个，
只有最后一个生效。

ONBUILD   构建下一级镜像生效

```
格式： ONBUILD <其它指令>
```

