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

慎用 docker commit

    docker commit --author "New Hello" --message "修改了默认网页" webserver nginx:v2

Dockerfile 定制镜像

    $ touch Dockerfile

内容为:

    FROM nginx
    RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html

FROM指定基础镜像   FROM scratch 从一个空白镜像开始

RUN    1. shell格式  2. exec格式  RUN ["可执行文件", "参数1", "参数2"]

```
docker run --link redis-master:redis-master -v /usr/local/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave1 -d redis redis-server /usr/local/etc/redis/redis.conf
docker run --link redis-master:redis-master -v /usr/local/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave2 -d redis redis-server /usr/local/etc/redis/redis.conf
docker run --link redis-master:redis-master -v /usr/local/redis/redis.conf:/usr/local/etc/redis/redis.conf --name redis-slave3 -d redis redis-server /usr/local/etc/redis/redis.conf
```




















