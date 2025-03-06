---
title: 使用docker启动mysql服务
date: 2025-02-28 19:00:39
tags: ["Hexo", "博客"]
categories: ["技术"]
top_img: /img/2.jpg
cover: https://th.bing.com/th/id/R.db9df5dc112132016f5c0d349fc1f27b?rik=VJoc8yLukZ96DA&riu=http%3a%2f%2fwww.yunweipai.com%2fwp-content%2fthemes%2fzYwp%2fimages%2fguide%2fdocker.png&ehk=eS6zmRRfkRxPIOxXNI8yfsUHh6R8Lq%2bOG%2fIpQ0VPfQQ%3d&risl=&pid=ImgRaw&r=0
---


# 前言
我强烈不推荐在Windows上安装MySQL，学习阶段我强烈推荐用**docker安装MySQL**，当时我在Windows上安装MySQL卡的我想哭，我今天用docker安装用了不到5分钟，几条命令就完成了，拉取镜像，创建容器就完成了，然后你就可以远程链接学习了，我真后悔没有早知道docker安装这么好了，而且docker安装的容错率是真高，学习难免命令输错，用docker安装，把MySQL玩炸了也没事，重建容器就行

# 安装docker

## 1. 前提条件

在 Windows 11 通过 WSL2 安装 Docker 需要满足以下条件：

-   运行 Windows 10 版本 1903 以上（推荐 Windows 11）
    
-   具有管理员权限
    
-   启用 WSL2 和虚拟机平台
    >**WSL2的安装步骤** [WSL2 Linux install | ZJNULL](https://zjnull.cn/linux/)
    


## 2. 安装 Docker Desktop

1.  访问 Docker 官网 https://www.docker.com/products/docker-desktop 下载 Docker Desktop 最新版本
    >[或点击这里跳转GitHub安装](https://github.com/tech-shrimp/docker_installer/releases/tag/latest)
    
    ![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228191701994.png)
2.  双击安装程序，并按照提示安装
	>想要指定安装位置可执行


	```
	start /w ""安装包目录"install --installation-dir=指定位置目录
	```

3.  安装完成后，重新启动 Docker Desktop，确认其正常运行
    

## 3. 配置 Docker 使用 WSL2

1.  打开 Docker Desktop，进入 **Settings（设置）**。
    
2.  在 **General（常规）** 选项中，确保启用了 "Use the WSL 2 based engine"。
    
3.  在 **Resources -> WSL Integration** 中，找到你的 Linux 发行版，并启用其 Docker 支持。
    
4.  点击 "Apply & Restart" 使设置生效。
    
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228192620409.png)



## 4. 验证 Docker 是否安装成功

1.  打开 WSL 终端（默认是 Ubuntu）。
    
2.  运行以下命令检查 Docker 版本：
    
    ```
    docker --version
    ```
    ![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228193252424.png)
3.  运行以下命令拉取并运行一个测试容器：
    
    ```
    docker run hello-world
    ```
    
    若输出包含 **Hello from Docker** 则说明安装成功
    
	-   这说明 **Docker 运行了一个 `hello-world` 容器**，并且这个容器内部的程序成功输出了这个消息。
	-   Docker 运行 `hello-world` 容器的步骤：
	    1.  **Docker 客户端** 联系 **Docker 后台守护进程**（daemon）。
	    2.  **守护进程拉取** `hello-world` 镜像（如果本地没有）。
	    3.  **创建并运行** 一个基于该镜像的容器。
	    4.  **容器输出信息**，并把它传回你的终端。
    

## 5. 解决可能遇到的问题

### 问题 1：WSL2 未安装或未启用

**解决方法：**

-   确保 Windows 已更新到最新版本。
    
-   运行 `wsl --update` 更新 WSL。
    
-   确保 WSL 2 内核更新包已安装。
    

### 问题 2：Docker 启动失败

**解决方法：**

-   进入 Docker Desktop 设置，确保已启用 WSL2 引擎。
    
-   运行 `wsl --set-default-version 2` 确保 WSL 2 是默认版本。
    
-   运行 `wsl --shutdown` 关闭所有 WSL 实例后重新打开 Docker。
    

### 问题 3：Docker 命令未找到

**解决方法：**

-   运行 `wsl -l -v` 确保 Docker 绑定的 Linux 发行版为 WSL2。
    
-   确保已在 WSL 终端中启用 Docker WSL2 集成。
    

## 6.基础语法
### 1. 查看正在运行的容器

使用以下命令查看当前正在运行的容器：

```
docker ps
```

该命令会列出所有正在运行的容器，包括容器 ID、镜像、创建时间、状态、端口等信息。

### 2. 查看所有容器（包括已停止的）

若要查看所有容器（包括已停止的容器），使用以下命令：

```
docker ps -a
```
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228194203936.png)
该命令会列出所有容器的详细信息，包括那些已经退出的容器。

### 3. 查看已有的镜像

Docker 镜像是容器的基础，使用以下命令查看本地已有的 Docker 镜像：

```
docker images
```

该命令会列出本地存储的所有 Docker 镜像，包含镜像 ID、仓库名称、标签、大小等信息。

### 4. 删除某个容器

如果某个容器不再需要，可以使用以下命令删除它：

```
docker rm <容器ID>
```

例如，删除 ID 为 `abcd1234` 的容器：

```
docker rm abcd1234
```

若容器正在运行，需要先停止它，再进行删除：

```
docker stop abcd1234
```

```
docker rm abcd1234
```

### 5. 删除某个镜像

若某个镜像不再使用，可以使用以下命令删除它：

```
docker rmi <镜像ID>
```

例如，删除 ID 为 `abcd5678` 的镜像：

```
docker rmi abcd5678
```

需要注意的是，若该镜像已被某个容器使用，则需要先删除相关容器才能成功删除该镜像。


## 7.拉取MySQL镜像
在 Docker 里安装 MySQL 主要是 **拉取官方 MySQL 镜像** 并 **运行一个容器**，可以按照以下步骤来安装和使用 MySQL

拉取一个 MySQL 的 Docker 镜像，可以使用以下命令：
```
docker pull mysql
```
该命令会从 Docker Hub 拉取最新的 MySQL 官方镜像。如果需要特定版本的 MySQL，可以指定版本号，例如：
```
docker pull mysql:8.0
```
其中 `8.0` 代表 MySQL 8.0 版本，你可以根据需求更换版本号，如 `5.7`、`8.1` 等。

拉取完成后，可以使用以下命令查看已下载的 MySQL 镜像：

```
docker images
```
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228195057838.png)

## 8.创建 MySQL 容器并指定端口
现在不需要挂载外部数据卷，创建一个简单的 MySQL 容器，可以使用以下命令：
```
docker run -d --name mysql-server -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:latest
```
这条命令做了以下几件事：

-   `-d`: 后台运行容器
-   `--name mysql-server`: 给容器指定一个名字 `mysql-server`
-   `-e MYSQL_ROOT_PASSWORD=root`: 设置 MySQL root 用户的密码为 `root`
-   `-p 3306:3306`: 将宿主机的 3306 端口映射到容器的 3306 端口
-   `mysql:latest`: 使用最新版本的 MySQL 镜像

执行这条命令后，MySQL 容器应该会启动并在后台运行。你可以使用 `docker ps` 查看容器的运行状态

## 9.访问并连接容器中的 MySQL 数据库

### 启动MySQL数据库容器
刚才，我们已经创建了一个名为**mysql-server**的容器，启动一个已存在的 Docker 容器，可以使用以下命令：

```
docker start <容器ID或容器名称>
```
例如，启动名为 `my-mysql` 的容器：

```
docker start my-mysql
```
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228195742389.png)
>请确保 Docker Desktop 正在运行。Docker Desktop 必须在后台运行，WSL 2 才能访问 Docker 服务
如果不知道容器 ID 或名称，可以先使用以下命令查看所有容器（包括已停止的）：

```
docker ps -a
```
### 连接MySQL数据库
可以直接进入 MySQL 容器内部，并使用 `mysql` 命令行工具：

```
docker exec -it mysql-server mysql -u root -p
```
然后输入 MySQL `root` 用户的密码（即启动容器时 `MYSQL_ROOT_PASSWORD` 设定的值）
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228200617414.png)
## 结论

这样，你可以在 Windows 上更高效地运行 MySQL 容器，而无需额外的虚拟机开销。