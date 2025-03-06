---
title: 利用Linux-bash将sql文件导入到docker的MySQL容器内
date: 2025-02-28 21:50:02
tags: ["Docker", "MySQL"]
categories: ["技术"]
cover: https://unsplash.com/photos/ZOQ5o-VYgag/download?ixid=M3wxMjA3fDB8MXxzZWFyY2h8MXx8bXlzcWx8ZW58MHx8fHwxNzQwNzY0MjE3fDA&force=true&fm=jpg
---

# 前言
通常MySQL **无法找到 `磁盘内的文件`**，是因为MySQL 运行在 Docker 容器中，它可能无法访问 `/mnt/c/...` 这个 WSL2 的挂载路径。容器**没有权限**读取 Windows 目录的 SQL 文件，MySQL 只能访问**容器内部的文件**，但我们的 `.sql` 文件在 Windows 目录里

![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228222304974.png)

在 Linux（包括 WSL2）环境下，使用 Bash 命令可以方便地将 SQL 文件导入 Docker 内运行的 MySQL 容器


## docker exec -it mysql-server bash

这个命令：

-   **进入 MySQL 容器的 Linux 终端（bash）**，你在里面可以执行各种 Linux 命令。
-   **然后你再手动运行 `mysql -u root -p`** 进入 MySQL 交互式模式。

步骤如下：

```
docker exec -it mysql-server bash   # 进入容器的 Linux 终端（shell）
mysql -u root -p                    # 进入 MySQL
```
>如果想**同时访问 Linux 文件系统和 MySQL**，就先进入 `bash`

你可以在 `bash` 里执行其他 Linux 命令，比如：

```
ls /var/lib/mysql
cat /etc/mysql/my.cnf
```
这些是 MySQL 数据库的配置文件和数据文件路径，只有 `bash` 能访问它们。

## docker exec -it mysql-server mysql -u root -p

这个命令：

-   **直接运行 `mysql` 客户端**，让你进入 MySQL 交互式模式。
-   **不经过 `bash`**，也就无法运行 Linux 命令（比如 `ls`、`cd`）。

如果**只是想操作数据库**，这个方法更直接
```
docker exec -it mysql-server mysql -u root -p
```

# 导入文件

## **1. 前置条件**

确保你的环境已满足以下条件：

-   已安装 **Docker** 并运行 Docker 守护进程 (`docker ps` 能正常执行)。
-   Docker 内部已运行一个 MySQL 容器（假设容器名称为 `mysql-server`）
-   已创建一个 MySQL 数据库（如 `test`）
-   SQL 文件（如 `DDL+drop.sql`）存放在 Linux 可访问的路径，比如：`/mnt/c/Users/Brian/Downloads/DDL+drop.sql  （WSL2） ~/Downloads/DDL+drop.sql                    （Linux）`
    

----------

## **方法一：直接用 `docker exec` 导入 SQL 文件**

**适用于：** 你的 SQL 文件存放在 Linux 可访问路径，直接执行导入。
```
docker exec -i mysql-server mysql -u root -p test < /mnt/c/Users/Brian/Downloads/DDL+drop.sql
```
然后输入 **MySQL root 密码**

**说明：**

-   `docker exec -i`：在容器内运行命令，并允许输入数据。
-   `mysql-server`：MySQL 容器名称。
-   `mysql -u root -p test`：使用 root 用户连接 `test` 数据库。
-   `< DDL+drop.sql`：将 SQL 文件内容传递给 `mysql` 命令执行。

----------

## **方法二：复制 SQL 文件到容器后导入**

**适用于：** 你的 SQL 文件在 Windows 或 Linux 目录中，MySQL 可能无法直接访问。

### **① 复制 SQL 文件到 MySQL 容器**



```
docker cp /mnt/c/Users/Brian/Downloads/DDL+drop.sql mysql-server:/DDL+drop.sql
```
这会将 `DDL+drop.sql` 文件复制到容器内 `/DDL+drop.sql` 目录
> 注意：docker cp 是 Docker 的命令，需要在操作系统的终端中运行，而不是在 MySQL 命令行中运行
### **② 进入 MySQL 容器**

```
docker exec -it mysql-server bash
```
### **③ 进入 MySQL 交互模式**

```
mysql -u root -p
```
输入 **MySQL root 密码**

### **④ 选择数据库**

```
USE test;
```
>如果sql文件内包含CREATE/USE数据库等操作，要注意CREATE的**数据库名称**，如果已存在相同的名称，MySQL会将他们**合并**
### **⑤ 执行 SQL 文件**

```
SOURCE /DDL+drop.sql;
```
![](https://ghfast.top/https://raw.githubusercontent.com/Brian510000/pic_bed/main/web-site/20250228221627713.png)


成功后，可以用：
```
SHOW TABLES;
```
检查是否导入成功

