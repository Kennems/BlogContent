---
title : 'Docker'
date : 2024-10-12T22:30:13+08:00
lastmod: 2024-10-12T22:20:13+08:00
description : "Docker"  
categories : ["Docker"]
tags : ["Docker"]
---

# Docker

快速构建、运行、管理应用的工具

Docker打印所有容器（包括关闭的）

```shell
docker ps -a
```

正在运行的

```shell
docker ps
```

删除容器

```shell
docker rm <container_id>
```

启动已有的容器

```shell
docker start <容器ID或名称>
```

启动容器并进入其交互式终端

```shell
docker start -i <容器ID或名称>
```

启动后直接进入容器的 bash 终端

```
docker exec -it <容器ID或名称> /bin/bash
```

停止容器

```shell
docker stop <container_id>
```

```shell
docker run -d \
--name mysql \
-p 3306:3306 \ 
-e TZ=Asia/Shanghai \
-e MYSQL_ROOT_PASSWORD=123 \
mysql
```

## Docker基础知识

![image-20240723192837909](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240723192837909.png)





## Docker 数据卷

作用：宿主机目录和docker容器内目录做一个关联



## MySql容器的数据挂载

```shell
docker run -d \
--name mysql \
-p 3307:3306 \
-e TZ=Asia/Shanghai \
-e MYSQL_ROOT_PASSWORD=123 \
-v /root/mysql/data:/var/lib/mysql \
-v /root/mysql/init:/docker-entrypoint-initdb.d \
-v /root/mysql/conf:/etc/mysql/conf.d \
mysql
```

## 自定义镜像

### DokerFile

DockerFile就是一个文本文件， 其中包括一个个的指令（Instruction），用指令来说明要执行什么操作来构建镜像。将来Docker可以根据DockerFile帮我们构建镜像。

![image-20240724145130585](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240724145130585.png)

## 网络

加入自定义网络的容器才可以通过容器互相访问， Docker的网络操作命令如下：

| 命令                                                         | 说明                                                      |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| `docker network ls`                                          | 列出所有 Docker 网络                                      |
| `docker network inspect <network>`                           | 显示指定网络的详细信息                                    |
| `docker network create <network>`                            | 创建一个新的 Docker 网络                                  |
| `docker network rm <network>`                                | 删除指定的 Docker 网络                                    |
| `docker network connect <network> <container>`               | 将容器连接到指定的网络                                    |
| `docker network disconnect <network> <container>`            | 将容器从指定的网络断开                                    |
| `docker network prune`                                       | 删除所有未使用的 Docker 网络                              |
| `docker run --network <network> <image>`                     | 运行容器并指定使用的网络                                  |
| `docker network create --driver <driver> <network>`          | 使用指定的驱动程序创建网络（bridge, overlay, host等）     |
| `docker network inspect --format <template> <network>`       | 以自定义格式显示网络的详细信息                            |
| `docker network connect --ip <ip> <network> <container>`     | 将容器连接到指定网络并分配静态 IP                         |
| `docker network create --subnet <subnet> <network>`          | 指定子网和 IP 范围创建网络（例：--subnet=192.168.1.0/24） |
| `docker network create --gateway <gateway> <network>`        | 创建网络时指定网关 IP                                     |
| `docker network disconnect -f <network> <container>`         | 强制将容器从指定的网络断开                                |
| `docker network connect --alias <alias> <network> <container>` | 将网络中的容器添加别名（便于服务发现）                    |

## 部署流程

1. 部署Java应用

2. 部署nginx

   - ```shell
     docker run -d \
         --name nginx \
         -p 18080:18080 \
         -p 18081:18081 \
         -v /root/nginx/html:/usr/share/nginx/html \
         -v /root/nginx/nginx.conf:/etc/nginx/nginx.conf \
         --network kennem \
         nginx
     ```



## DockerCompose

![image-20240726213413347](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240726213413347.png)

`DockerCompose`通过一个单独的`docker-compose.yml`模板文件（YAML格式）来定义一组相关联的应用容器， 帮助我们实现多个相互关联的Docker容器的快速部署。

```yaml
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on:
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall
```

