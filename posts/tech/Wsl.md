---
title : 'Wsl'
date : 2025-10-23T15:40:01+08:00
lastmod: 2025-10-23T15:40:01+08:00
description : "Wsl初始化" 
image : img/cat.jpg
draft : false    
categories : ["Linux"]
tags : ["Wsl"]
---

# Wsl

## 初始化 su 用户（WSL）

WSL 默认未启用 root 密码，可直接使用 `sudo` 获得管理员权限。

### 方法 1：直接切到 root（推荐）

```bash
sudo su
# 或
sudo -i
```

------

### 方法 2：设置 root 密码（启用 su）

```bash
sudo su
passwd   # 设置 root 密码
exit
su       # 输入新密码登录 root
```

------

### 方法 3：设置默认登录用户为 root

查看发行版名：

```bash
wsl -l -v
```

设置：

```bash
ubuntu config --default-user root
```

恢复：

```bash
ubuntu config --default-user kennem
```

## 更新系统基础软件源（Ubuntu官方→国内源）

### 1. 备份原有软件源

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

### 2. 修改为国内镜像源（以阿里云为例）

编辑：

```bash
sudo vim /etc/apt/sources.list
```

替换内容为（Ubuntu 22.04 示例）：

```bash
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
```

保存并更新：

```bash
sudo apt update && sudo apt upgrade -y
```

## 安装 Docker（使用官方推荐方式）

### 1. 卸载旧版本（如果有）

```bash
sudo apt-get remove -y docker docker-engine docker.io containerd runc || true
```

### 2. 安装依赖

```bash
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release -y
```

### 3. 添加官方 GPG 密钥

```bash
sudo mkdir -p /usr/share/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-ce-archive-keyring.gpg
```

### 4. 添加 Docker 软件源（可切换为国内镜像）

官方源：

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

国内镜像源（推荐）：

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce-archive-keyring.gpg] \
  https://mirrors.aliyun.com/docker-ce/linux/ubuntu jammy stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list
```

### 5. 安装 Docker

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 6. 验证安装

```bash
docker --version
docker compose version
sudo systemctl enable --now docker
```

## 配置 Docker Hub 镜像加速器

Docker Hub 默认从国外下载，建议添加国内加速器：

### 添加加速器列表（推荐组合）

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": [
    "https://registry.cn-hangzhou.aliyuncs.com",
    "https://docker.mirrors.ustc.edu.cn",
    "https://mirror.ccs.tencentyun.com",
    "https://docker.m.daocloud.io"
  ]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 验证是否生效

```bash
docker info | grep -A 3 "Registry Mirrors"
```

输出类似：

```bash
 Registry Mirrors:
  https://registry.cn-hangzhou.aliyuncs.com/
  https://docker.m.daocloud.io/
```

说明生效。

## 添加普通用户权限

###  创建 Docker 用户组（如果不存在）

```bash
sudo groupadd docker
```

> 大部分系统安装 Docker 时已经自动创建了 `docker` 组，如果存在会提示 `groupadd: group 'docker' already exists`，可以忽略。

------

###  将当前用户加入 Docker 组

```bash
sudo usermod -aG docker $USER
```

- `-aG`：追加当前用户到指定组，不覆盖原有组
- `$USER`：当前登录用户名，也可以直接写 `kennem`

------

###  重新登录用户或刷新组权限

```bash
newgrp docker
```

或者 **完全登出再登录**，才能使组权限生效。

------

###  验证是否生效

```bash
docker ps
```

- 如果能够列出运行的容器而 **不需要 sudo**，说明成功。
- 如果仍提示权限错误，请确认 `$USER` 已在 `docker` 组：

```bash
groups $USER
```

------

###  注意事项

1. **安全性**：加入 `docker` 组等同于拥有 root 权限，因为 Docker 可以执行任意宿主机命令。
2. **系统服务**：Docker 仍然以 root 启动，但你作为普通用户可以无需 sudo 使用 Docker CLI。
3. **脚本自动化**：加入组后，所有 Docker 命令无需 sudo，例如：

```bash
docker run hello-world
docker pull mysql:8.0
docker-compose up -d
```

## 部署MySQL

### **创建本地持久化数据目录**

```bash
mkdir -p /data/mysql
```

- 这样容器删除后数据仍保留。

### 拉取 MySQL 镜像

```bash
docker pull mysql:8.0
```

- `mysql:8.0` 可替换为其他版本，如 `mysql:5.7`
- 国内用户建议使用国内镜像加速：

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/library/mysql:8
```

### 启动 MySQL 容器

```bash
docker run -d \
  --name mysql-server \
  -e MYSQL_ROOT_PASSWORD=Root1234 \
  -e MYSQL_DATABASE=testdb \
  -e MYSQL_USER=testuser \
  -e MYSQL_PASSWORD=testpass \
  -p 3306:3306 \
  -v /data/mysql:/var/lib/mysql \
  mysql:8.0
```

#### 参数说明：

| 参数                              | 说明                     |
| --------------------------------- | ------------------------ |
| `--name mysql-server`             | 容器名称                 |
| `-e MYSQL_ROOT_PASSWORD=Root1234` | 设置 root 密码           |
| `-e MYSQL_DATABASE=testdb`        | 容器启动时创建的数据库   |
| `-e MYSQL_USER=testuser`          | 容器启动时创建的用户     |
| `-e MYSQL_PASSWORD=testpass`      | 用户密码                 |
| `-p 3306:3306`                    | 映射宿主机端口到容器端口 |
| `-v /data/mysql:/var/lib/mysql`   | 数据持久化挂载           |

### 验证 MySQL 容器运行

1. 查看容器状态：

```bash
docker ps
```

1. 登录 MySQL：

```bash
docker exec -it mysql-server mysql -uroot -p
# 输入密码 Root1234
```

1. 测试数据库：

```bash
SHOW DATABASES;
```

------

### 停止/启动/重启容器

```bash
# 停止
docker stop mysql-server
# 启动
docker start mysql-server
# 重启
docker restart mysql-server
```

------

### 删除 MySQL 容器

1. **停止容器**

```bash
docker stop mysql-server
```

1. **删除容器**

```bash
docker rm mysql-server
```

1. **删除镜像（如果不再使用）**

```bash
docker rmi mysql:8.0
```

> 注意：删除容器不会删除 `/data/mysql` 数据目录，如果想连数据一起删除：

```bash
sudo rm -rf /data/mysql
```

------

### 使用 Docker Compose 部署（可选）

创建 `docker-compose.yml`：

```bash
version: "3.9"
services:
  mysql:
    image: mysql:8.0
    container_name: mysql-server
    environment:
      MYSQL_ROOT_PASSWORD: Root1234
      MYSQL_DATABASE: testdb
      MYSQL_USER: testuser
      MYSQL_PASSWORD: testpass
    ports:
      - "3306:3306"
    volumes:
      - /data/mysql:/var/lib/mysql
```

启动：

```bash
docker-compose up -d
```

停止并删除：

```bash
docker-compose down
```

- 数据仍在 `/data/mysql` 持久化。

## 部署Redis

### **创建本地持久化数据目录**

```bash
mkdir -p /data/redis
```

- 容器删除后数据仍保留。

### 拉取 Redis 镜像

```
docker pull redis:8.2
```

- `redis:8.2` 可根据需求选择版本
- 国内用户可用国内镜像加速：

```
docker pull registry.cn-hangzhou.aliyuncs.com/official/redis:8.2
```

------

### 启动 Redis 容器

```bash
docker run -d \
  --name redis-server \
  -p 6379:6379 \
  -v /data/redis:/data \
  redis:8.2 \
  redis-server --appendonly yes
```

### 参数说明

| 参数                            | 说明                     |
| ------------------------------- | ------------------------ |
| `--name redis-server`           | 容器名称                 |
| `-p 6379:6379`                  | 映射宿主机端口到容器端口 |
| `-v /data/redis:/data`          | 持久化数据挂载           |
| `redis-server --appendonly yes` | 开启 AOF 持久化          |

> 默认 Redis 不设置密码，如果需要可以加：

```
-e REDIS_PASSWORD=MyStrongPass
```

然后用 `redis-cli -a MyStrongPass` 登录。

### 验证 Redis 容器运行

1. 查看容器状态：

```
docker ps
```

1. 进入容器：

```bash
docker exec -it redis-server redis-cli
```

1. 测试：

```bash
set testkey hello
get testkey
```

------

### 停止/启动/重启容器

```bash
# 停止
docker stop redis-server

# 启动
docker start redis-server

# 重启
docker restart redis-server
```

------

### 删除 Redis 容器

1. **停止容器**

```bash
docker stop redis-server
```

1. **删除容器**

```bash
docker rm redis-server
```

1. **删除镜像（可选）**

```bash
docker rmi redis:8.2
```

1. **删除数据目录（可选）**

```bash
sudo rm -rf /data/redis
```

------

### 使用 Docker Compose 部署 Redis（可选）

创建 `docker-compose.yml`：

```bash
version: "3.9"
services:
  redis:
    image: redis:8.2
    container_name: redis-server
    ports:
      - "6379:6379"
    volumes:
      - /data/redis:/data
    command: ["redis-server", "--appendonly", "yes"]
```

启动：

```bash
docker-compose up -d
```

停止并删除：

```bash
docker-compose down
```

- 数据仍在 `/data/redis` 持久化。

------

### 端口冲突和权限注意

如果宿主机 6379 被占用，使用不同端口：

```
-p 6380:6379
```

## 部署ES

**创建本地持久化数据目录**

```bash
mkdir -p /data/elasticsearch
```

- 用于存储 Elasticsearch 的数据文件，即使容器删除，数据仍然保留。

------

### 拉取 Elasticsearch 镜像

```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.10.2
```

- 最新版本可根据官网调整
- 国内用户建议使用国内镜像加速：

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/library/elasticsearch:8.10.2
```

------

### 启动 Elasticsearch 容器

Elasticsearch 对系统资源有要求，需要设置内存和一些环境变量：

```yaml
docker run -d \
  --name es-server \
  -p 9200:9200 \
  -p 9300:9300 \
  -e "discovery.type=single-node" \
  -e "ES_JAVA_OPTS=-Xms1g -Xmx1g" \
  -v /data/elasticsearch:/usr/share/elasticsearch/data \
  docker.elastic.co/elasticsearch/elasticsearch:8.10.2
```

#### 参数说明

| 参数                                                   | 说明                           |
| ------------------------------------------------------ | ------------------------------ |
| `--name es-server`                                     | 容器名称                       |
| `-p 9200:9200`                                         | 对外 HTTP 接口                 |
| `-p 9300:9300`                                         | 节点间通信端口                 |
| `-e discovery.type=single-node`                        | 单节点模式                     |
| `-e ES_JAVA_OPTS=-Xms1g -Xmx1g`                        | JVM 内存设置，可根据宿主机调整 |
| `-v /data/elasticsearch:/usr/share/elasticsearch/data` | 数据持久化挂载                 |

------

### 验证 Elasticsearch 容器运行

1. 查看容器状态：

```bash
docker ps
```

1. 测试 HTTP 接口：

```bash
curl http://localhost:9200
```

示例返回：

```bash
{
  "name" : "es-server",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "abcd1234",
  "version" : {
    "number" : "8.10.2",
    ...
  },
  "tagline" : "You Know, for Search"
}
```

------

### 停止/启动/重启容器

```bash
# 停止
docker stop es-server

# 启动
docker start es-server

# 重启
docker restart es-server
```

------

### 删除 Elasticsearch 容器

1. **停止容器**

```bash
docker stop es-server
```

1. **删除容器**

```bash
docker rm es-server
```

1. **删除镜像（可选）**

```bash
docker rmi docker.elastic.co/elasticsearch/elasticsearch:8.10.2
```

1. **删除数据目录（可选）**

```bash
sudo rm -rf /data/elasticsearch
```

------

### 使用 Docker Compose 部署 Elasticsearch（可选）

创建 `docker-compose.yml`：

```yaml
version: "3.9"
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.10.2
    container_name: es-server
    environment:
      - discovery.type=single-node
      - ES_JAVA_OPTS=-Xms1g -Xmx1g
    ports:
      - "9200:9200"
      - "9300:9300"
    volumes:
      - /data/elasticsearch:/usr/share/elasticsearch/data
```

启动：

```bash
docker-compose up -d
```

停止并删除：

```bash
docker-compose down
```

- 数据仍在 `/data/elasticsearch` 持久化。

------

### 端口冲突与权限注意

1. \**端口冲突**
   - 宿主机 9200 或 9300 被占用，可用不同端口：

```bash
-p 9201:9200 -p 9301:9300
```