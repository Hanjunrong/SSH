# Docker 命令速查手册

Docker 包括三个基本概念

### 镜像(Images)

### 仓库(Container)

### 容器(Repository)



### 启动命令  

```
service docker start 
```

^ 建立docker组: groupadd docker

^ 将当前用户加入docker组: usermod -aG docker $USER

### 获取镜像

```
docker pull [仓库名]
```

### 启动容器

```
docker run -it  ubuntu:14.04 bash
```

-it 交互式终端

--rm 容器退出后删除

bash 交互式shell

example : $ docker run --name webserver -d -p 80:80 nginx

### 列出镜像

```
docker images
```

自定义列表 docker images --format "table {{.ID}}\t{{.Reposity}}\t{{.Tag}}"

### 删除镜像

```
docker rmi 
```

删除所有 docker rmi $(docker images -q)











