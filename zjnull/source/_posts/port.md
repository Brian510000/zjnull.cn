---
title: SQL端口冲突
date: 2025-02-28 20:09:57
tags: ["Hexo", "博客"]
categories: ["技术"]
cover: https://cdn.pixabay.com/photo/2016/12/09/18/30/database-schema-1895779_1280.png
---


### **1. 端口映射格式**

使用 `-p` 参数进行端口映射，格式如下：

`-p 宿主机端口:容器端口`

-   **宿主机端口**：指宿主机（例如 Windows 或 WSL2）上的端口
-   **容器端口**：指 MySQL 容器内部的端口。MySQL 默认运行在容器的 `3306` 端口，但宿主机可以将其映射到不同的端口

----------

### **2. 为什么用 `3307:3306` 而不是 `3306:3306`？**

#### **情况 1：宿主机上已经有 MySQL 在 `3306` 端口运行**

如果宿主机上已经有 MySQL 或其他程序占用了 `3306` 端口，那么：

-   使用 `-p 3306:3306` 会导致端口冲突，Docker 容器无法启动。
-   解决方法：使用 `-p 3307:3306`，这样容器内部 MySQL 仍然在 `3306` 端口运行，但外部通过宿主机的 `3307` 端口进行连接。

#### **情况 2：你想运行多个 MySQL 容器**

每个容器需要一个独立的宿主机端口：

```
docker run -d --name mysql1 -p 3307:3306 -e MYSQL_ROOT_PASSWORD=root mysql
docker run -d --name mysql2 -p 3308:3306 -e MYSQL_ROOT_PASSWORD=root mysql
```
-   **`localhost:3307`** 连接到 `mysql1`
-   **`localhost:3308`** 连接到 `mysql2`

----------

### **3. 如何连接 3307 端口的 MySQL**

如果你在宿主机（Windows 或 WSL2）上使用 `3307` 端口连接 MySQL：

#### 使用命令行：

```
mysql -h 127.0.0.1 -P 3307 -u root -p
```
#### 使用图形化工具（如 Navicat 或 MySQL Workbench）：

-   **主机**：`127.0.0.1`
-   **端口**：`3307`
-   **用户名**：`root`
-   **密码**：`root`

----------

### **4. 查看端口使用情况**

#### **在 Windows 上查看端口使用情况**

##### 使用 `netstat` 查看所有活动连接和端口占用：
```
netstat -ano
```
##### 查看特定端口（如 `3306`）是否被占用：

```powershell
netstat -ano | findstr :3306
```
##### 使用 PowerShell 查看端口占用：
```powershell
Get-NetTCPConnection | Where-Object { $_.State -eq 'Listen' }
```
#### **在 WSL2 中查看端口使用情况**

##### 使用 `netstat`：

首先安装 `net-tools`：
```
sudo apt-get install net-tools
```
然后查看端口占用：

```
netstat -tuln
```
##### 使用 `ss`（推荐）：
```
ss -tuln
```
#### **查看 Docker 容器占用的端口**

查看当前运行的 Docker 容器和端口映射：

```
docker ps
```
输出示例：
```nginx
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS          PORTS                    NAMES
abcd1234       mysql:latest  "..."      2 hours ago      Up 2 hours      0.0.0.0:3306->3306/tcp   mysql-container
```
表示容器 `mysql-container` 映射了宿主机的 `3306` 端口。

----------

### **5. 解决端口冲突**

#### **如果宿主机上已经有服务占用了 `3306` 端口**

如果你要运行的 MySQL 容器端口与宿主机上的端口冲突，可以使用不同的端口进行映射。例如：

```
docker run -d -p 3307:3306 --name mysql-container -e MYSQL_ROOT_PASSWORD=root mysql
```
这样，MySQL 容器内部仍然使用 `3306` 端口，外部通过宿主机的 `3307` 端口访问。

#### **如果 Docker 已经占用了 `3306` 端口**

你可以停止容器或修改端口映射：
```
docker stop mysql-container docker rm mysql-container
docker run -d -p 3307:3306 --name mysql-container -e MYSQL_ROOT_PASSWORD=root mysql
```
这样，容器的 `3306` 端口映射到宿主机的 `3307` 端口，避免冲突。

----------

### **总结**

-   **端口映射**：通过 `-p 宿主机端口:容器端口` 来进行端口映射，避免冲突
-   **查看端口使用情况**：可以通过 `netstat` 或 `ss` 查看 Windows 和 WSL2 的端口占用情况
-   **解决端口冲突**：通过修改 Docker 容器的端口映射，避免与宿主机已有服务冲突