---
title: MySQL 第四第五周
date: 2025-03-14 20:48:42
tags: ["笔记", "MySQL"]
categories: ["技术"]
toc: true
comments: true
---
# 用户、角色

## 1. **用户管理**

-   **创建用户**
    
    ```sql
    CREATE USER '用户名'@'主机' IDENTIFIED BY '密码';
    ```
    
    示例：
    
    ```sql
    CREATE USER 'read_user1'@'localhost' IDENTIFIED BY '1234';
    ```
    

    >   `@'localhost'` 不能省略，表示该用户仅能从本机登录。如果省略 `IDENTIFIED BY '密码'`，则创建的用户没有密码，登录时可能受限。
-   **查看用户权限**
    
    ```sql
    SHOW GRANTS FOR '用户名'@'主机';
    ```
    
    示例：
    
    ```sql
    SHOW GRANTS FOR 'read_user1'@'localhost';
    ```
    

## 2. **权限管理**

-   **授予权限**
    
    ```sql
    GRANT 权限 ON 数据库.表 TO '用户名'@'主机';
    ```
    
    示例：
    
    ```sql
    GRANT SELECT ON example.emp TO 'read_user1'@'localhost';
    GRANT UPDATE ON example.emp TO 'read_user1'@'localhost';
    ```
    

    
    >   `ON example.*` 作用于整个数据库，`ON example.emp` 仅作用于 `emp` 表
    >   `SELECT` 权限仅允许查询，`UPDATE` 允许修改数据但不能插入新数据
-   **撤销权限**
    
    ```sql
    REVOKE 权限 ON 数据库.表 FROM '用户名'@'主机';
    ```
    
    示例：
    
    ```sql
    REVOKE UPDATE ON example.emp FROM 'read_user1'@'localhost';
    ```
    
    
    >   `REVOKE` 只能撤销已有权限，否则会报错。

## 3. **角色管理**

-   **创建角色**
    
    ```sql
    CREATE ROLE '角色名';
    ```
    
    示例：
    
    ```sql
    CREATE ROLE 'app_developer';
    CREATE ROLE 'app_read';
    CREATE ROLE 'app_write';
    ```
    
-   **赋予角色权限**
    
    ```sql
    GRANT 权限 ON 数据库.表 TO '角色名';
    ```
    
    示例：
    
    ```sql
    GRANT ALL PRIVILEGES ON example.* TO 'app_developer';
    GRANT SELECT ON example.* TO 'app_read';
    ```

    
    >   `ALL PRIVILEGES` 赋予所有权限，谨慎使用。
-   **将角色赋予用户**
    
    ```sql
    GRANT '角色名' TO '用户名'@'主机';
    ```
    
    示例：
    
    ```sql
    GRANT 'app_developer' TO 'read_user1'@'localhost';
    GRANT 'app_read' TO 'user2'@'localhost';
    ```

    
    >   赋予角色后，用户不会自动继承权限，需要 `SET ROLE` 或者 `SET DEFAULT ROLE`。

----------

## **函数参数格式示例**

```sql
GRANT 权限 ON 数据库.表 TO '用户名'@'主机';
```


**示例**

```sql
GRANT SELECT, INSERT ON example.orders TO 'user1'@'localhost';
```


-   `SELECT, INSERT`：允许 `user1` 读取和插入 `orders` 表的数据。
-   `example.orders`：权限作用范围。
-   `'user1'@'localhost'`：仅允许 `user1` 在本机访问数据库。


## **`SET ROLE` 与 `CURRENT_ROLE()` **

### **1. `SELECT CURRENT_ROLE();`**

```sql
SELECT CURRENT_ROLE();
```

-   `CURRENT_ROLE()` 用于查询当前会话正在使用的角色。
-   如果用户没有设置角色，`CURRENT_ROLE()` 可能返回 `NULL` 或空值

**示例**

```sql
mysql> SELECT CURRENT_ROLE();
+---------------+
| CURRENT_ROLE()|
+---------------+
| NULL          |
+---------------+
```

>   `NULL` 表示当前用户没有主动启用任何角色，即使之前被授予了角色。

----------

### **2. `SET ROLE '角色名';`**

```sql
SET ROLE 'app_read';
```

-   `SET ROLE` 用于在当前会话中启用特定角色，使其权限生效。
-   只有 `GRANT` 过的角色才能 `SET ROLE`。
-   角色权限不会自动激活，必须 `SET ROLE` 后才能生效。

**示例**

```sql
mysql> SET ROLE 'app_read';
Query OK, 0 rows affected (0.01 sec)
```

>   这表示 `app_read` 角色已经启用，当前会话可使用 `app_read` 角色的权限。

----------

### **3. 再次查询 `CURRENT_ROLE()`**

```sql
SELECT CURRENT_ROLE();
```

**示例**

```sql
mysql> SELECT CURRENT_ROLE();
+---------------+
| CURRENT_ROLE()|
+---------------+
| app_read      |
+---------------+
```

>   说明当前会话的角色已变为 `app_read`，用户的权限也随之变更。

----------


1.  **角色不会自动生效**
    
    -   即使 `GRANT 'app_read' TO 'user2'@'localhost';` 赋予了角色，`user2` 也不能直接使用其权限，必须执行 `SET ROLE 'app_read';`
2.  **`SET ROLE` 仅在当前会话生效**
    
    -   `SET ROLE` 只在当前数据库连接会话内生效，重新连接后需要再次执行
3.  **默认角色设置**
    
    -   角色可以被设置为默认角色，使用户每次登录时自动使用：
        
        ```sql
        SET DEFAULT ROLE 'app_read' TO 'user2'@'localhost';
        ```
        

----------

## **完整示例**

```sql
-- 创建用户
CREATE USER 'user2'@'localhost' IDENTIFIED BY '1234';

-- 创建角色
CREATE ROLE 'app_read';

-- 赋予角色权限
GRANT SELECT ON example.* TO 'app_read';

-- 赋予角色给用户
GRANT 'app_read' TO 'user2'@'localhost';

-- 查询当前角色（默认未启用）
SELECT CURRENT_ROLE(); -- 可能返回 NULL

-- 启用角色
SET ROLE 'app_read';

-- 再次查询当前角色（现在应该返回 app_read）
SELECT CURRENT_ROLE();
```


| 操作                                             | 是否持久化 | 重新登录是否生效       |
|--------------------------------------------------|------------|------------------------|
| `GRANT 'app_read' TO 'user2'@'localhost';`       | ✅ 持久化   | ❌ 需要手动 `SET ROLE` |
| `SET ROLE 'app_read';`                           | ❌ 仅当前会话有效 | ❌ 需要重新执行       |
| `SET DEFAULT ROLE 'app_read' TO 'user2'@'localhost';` | ✅ 持久化   | ✅ 登录自动生效        |


1. **`GRANT`**：将角色授予用户，权限信息会持久化到数据库中，但用户登录后需要手动激活角色（通过 `SET ROLE`）。
2. **`SET ROLE`**：仅对当前会话有效，重新登录后需要再次执行。
3. **`SET DEFAULT ROLE`**：将角色设置为用户的默认角色，权限信息持久化，且用户登录时会自动激活该角色。


# 存储过程、触发器



## 一、存储过程（Stored Procedure）

### 1. **存储过程的定义**
存储过程是一段预编译的 SQL 代码，可以接受输入参数并返回输出结果。它的作用是封装复杂的 SQL 逻辑，方便重复调用。

### 2. **代码解析**
```sql
DELIMITER //  -- 修改语句结束符为 //，以便定义存储过程
CREATE PROCEDURE getsalary(IN empcode INT, OUT salary DOUBLE)
BEGIN
    -- 查询 emp 表中 empno 等于 empcode 的员工的 sal，并将结果赋值给 salary
    SELECT sal INTO salary FROM emp WHERE empno = empcode;
END //
DELIMITER ;  -- 恢复语句结束符为 ;
```


  > `IN empcode INT`：输入参数，表示员工的编号。
  > `OUT salary DOUBLE`：输出参数，用于返回员工的工资。
- **功能**：
  - 根据输入的员工编号 (`empcode`)，查询 `emp` 表中的 `sal` 字段，并将结果赋值给输出参数 `salary`。

### 3. **调用存储过程**
```sql
CALL getsalary(1001, @salary);  -- 假设员工编号为 1001
SELECT @salary;  -- 查看输出结果
```

---

## 二、触发器（Trigger）

### 1. **触发器的定义**
触发器是一种特殊的存储过程，它在特定事件（如 `INSERT`、`UPDATE`、`DELETE`）发生时自动执行。

### 2. **代码解析**

#### （1）`emp_after_delete` 触发器
```sql
CREATE TRIGGER emp_after_delete
AFTER DELETE ON emp  -- 在 emp 表执行 DELETE 操作后触发
FOR EACH ROW  -- 对每一行数据生效
BEGIN
    -- 将删除的数据插入到 emp_his 表中
    INSERT INTO emp_his(deptno, empno, ename, job, mgr, sal, comm, hiredate)
    VALUES(OLD.deptno, OLD.empno, OLD.ename, OLD.job, OLD.mgr, OLD.sal, OLD.comm, OLD.hiredate);
END
```

- **功能**：
  - 当从 `emp` 表中删除一行数据时，触发器会自动将这行数据插入到 `emp_his` 表中（用于记录删除的历史数据）。

 > `OLD`：表示被删除的行的旧数据。

#### （2）`takes_after_insert` 触发器
```sql
CREATE TRIGGER takes_after_insert
AFTER INSERT ON takes  -- 在 takes 表执行 INSERT 操作后触发
FOR EACH ROW  -- 对每一行数据生效
BEGIN
    -- 更新 student 表中的 tot_cred 字段
    UPDATE student
    SET tot_cred = tot_cred + (SELECT credits FROM course WHERE course_id = NEW.course_id)
    WHERE id = NEW.id;
END
```

- **功能**：
  - 当向 `takes` 表中插入一行数据时，触发器会根据插入的 `course_id` 查询 `course` 表中的学分 (`credits`)，并将该学分累加到 `student` 表的 `tot_cred` 字段中。

> `NEW`：表示新插入的行数据。

---

## 三、变量

### 1. **变量赋值与查询**
```sql
USE EXAMPLE;  -- 切换到 EXAMPLE 数据库
SET @deptcode = 1;  -- 定义变量 @deptcode 并赋值为 1
SET @personnum = 0;  -- 定义变量 @personnum 并赋值为 0

-- 查询 emp 表中 deptno 等于 @deptcode 的记录数，并将结果赋值给 @personnum
SELECT COUNT(*) INTO @personnum FROM emp WHERE deptno = @deptcode;

-- 查看 @personnum 的值
SELECT @personnum;
```

- **功能**：
  - 统计 `emp` 表中 `deptno` 为 1 的员工数量，并将结果存储在变量 `@personnum` 中。

### 2. **`DELIMITER` 的使用**
- `DELIMITER //`：将语句结束符临时改为 `//`，以便定义存储过程或触发器。
- `DELIMITER ;`：恢复语句结束符为 `;`。



## 四、总结

1. **存储过程**：
   - 封装 SQL 逻辑，方便重复调用。
   - 示例：`getsalary` 存储过程用于查询员工工资。

2. **触发器**：
   - 在特定事件（如 `INSERT`、`DELETE`）发生时自动执行。
   - 示例：
     - `emp_after_delete`：记录删除的员工数据。
     - `takes_after_insert`：更新学生的总学分。

3. **变量与查询**：
   - 使用 `SET` 定义变量，`SELECT ... INTO` 将查询结果赋值给变量。

4. **`DELIMITER`**：
   - 用于修改语句结束符，以便定义存储过程或触发器。

