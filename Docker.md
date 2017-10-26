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









