---
title: mysql
date: 2025-02-21 20:48:42
tags:
---


# MySQL 课堂笔记

## 1. 连接 MySQL 数据库

使用 root 用户连接 MySQL：
```sh
mysql -u root -p
```
输入密码后，即可进入 MySQL 命令行模式。
>机房的用户名账号密码都为root
## 2. 查看数据库
```sql
SHOW DATABASES;
```

## 3. 创建数据库
```sql
CREATE DATABASE mydb;
```

## 4. 使用数据库
```sql
USE mydb;
```

## 5. 创建表
```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    age INT NOT NULL
);
```

## 6. 查看表
```sql
SHOW TABLES;
```

## 7. 查看表结构
```sql
DESC users;
```

## 8. 插入数据
```sql
INSERT INTO users (name, age) VALUES ('Alice', 25), ('Bob', 30);
```

## 9. 查询数据
```sql
SELECT * FROM users;
```

## 10. 更新数据
```sql
UPDATE users SET age = 26 WHERE name = 'Alice';
```

## 11. 删除数据
```sql
DELETE FROM users WHERE name = 'Bob';
```

## 12. 删除表
```sql
DROP TABLE users;
```

## 13. 删除数据库
```sql
DROP DATABASE mydb;
```

## 14. 退出 MySQL
```sql
EXIT;
