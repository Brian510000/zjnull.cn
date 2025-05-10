---
title: MySQL 前三周
date: 2025-03-7 20:48:42
tags: ["笔记", "MySQL"]
categories: ["技术"]
toc: true
comments: true
---

# MySQL

# 第一周

## **一、数据库连接与基础操作**
1. **连接 MySQL**  
   ```bash
   mysql -u root -p  # 输入密码后进入命令行
   ```
   - **注意**：`-u` 后接用户名，`-p` 表示需要密码（输入后无回显）

2. **退出 MySQL**  
   ```sql
   EXIT;  -- 或使用 \q、QUIT
   ```

---

## **二、数据库操作**
1. **查看所有数据库**  
   ```sql
   SHOW DATABASES;  -- 显示所有数据库列表
   ```

2. **创建数据库**  
   ```sql
   CREATE DATABASE mydb;  -- 数据库名需唯一，区分大小写（取决于系统配置）
   ```

3. **删除数据库**  
   ```sql
   DROP DATABASE mydb;  -- 慎用！会永久删除数据库及数据
   ```

4. **切换数据库**  
   ```sql
   USE mydb;  -- 后续操作默认在此数据库执行
   ```

---

## **三、表操作**
1. **创建表**  
   ```sql
   CREATE TABLE users (
     id INT AUTO_INCREMENT PRIMARY KEY,  -- 自增主键（必须为主键或唯一索引）
     name VARCHAR(50) NOT NULL,          -- VARCHAR需指定长度，NOT NULL约束
     age INT NOT NULL
   );
   ```
   - **易错点**：  
     - `AUTO_INCREMENT` 必须与主键/唯一索引搭配使用。  
     - `VARCHAR(50)` 必须指定长度，超出部分会被截断。  

2. **查看表结构**  
   ```sql
   DESC users;  -- 显示字段名、类型、约束等元信息
   ```

3. **删除表**  
   ```sql
   DROP TABLE users;  -- 慎用！删除表及所有数据
   ```

4. **查看所有表**  
   ```sql
   SHOW TABLES;  -- 需先 USE 数据库
   ```

---

## **四、数据操作（CRUD）**
1. **插入数据**  
   ```sql
   INSERT INTO users (name, age) VALUES ('Alice', 25), ('Bob', 30);  -- 多值插入
   ```
   - **注意**：  
     - 字段顺序需与 VALUES 中值顺序一致。  
     - 自增字段（如 `id`）无需手动插入。  

2. **查询数据**  
   ```sql
   SELECT * FROM users;  -- 查询所有字段
   ```
   - **易错点**：`*` 可能导致查询效率低下，建议明确指定字段。

3. **更新数据**  
   ```sql
   UPDATE users SET age = 26 WHERE name = 'Alice';  -- WHERE 条件必须明确！
   ```
   - **重点强调**：  
     - 无 `WHERE` 条件的 `UPDATE` 会更新全表数据！  
     - 字符串条件需用单引号（`'Alice'`）。

4. **删除数据**  
   ```sql
   DELETE FROM users WHERE name = 'Bob';  -- WHERE 条件必须明确！
   ```
   - **重点强调**：  
     - 无 `WHERE` 条件的 `DELETE` 会清空整张表！  
     - 与 `DROP TABLE` 不同，`DELETE` 只删数据，不删表结构。

---

## **五、易混淆知识点**
1. **`DROP` vs `DELETE` vs `TRUNCATE`**  
   - `DROP TABLE`: 删除表结构及数据。  
   - `DELETE FROM`: 删除数据，保留表结构（可回滚）。  
   - `TRUNCATE TABLE`: 快速清空表数据（不可回滚，重置自增值）。

2. **`CHAR` vs `VARCHAR`**  
   - `CHAR(50)`: 固定长度，不足补空格，适合短且长度固定的数据（如邮编）。  
   - `VARCHAR(50)`: 可变长度，按实际长度存储，适合长度不定的数据（如用户名）。

3. **`AUTO_INCREMENT` 使用限制**  
   - 每个表只能有一个自增列。  
   - 需为整数类型（如 `INT`, `BIGINT`）。



# 第二周


## **一、基础查询语法**
1. **SELECT 基本结构**  
   ```sql
   SELECT 列名1, 列名2 FROM 表名 [WHERE 条件];
   ```
   - 示例：`select ID,name,salary from instructor;`
   - **注意**：`SELECT *` 会返回所有列，可能影响性能（需谨慎使用）

2. **WHERE 条件过滤**  
   ```sql
   WHERE dept_name='Physics'
   ```
   - **易错点**：字符串值必须用单引号 `''`，如 `'Physics'`（错误示例：`'Pyhsics'` 导致空结果）。
   - **注意**：条件值大小写敏感（如 `'Fall'` vs `'FALL'`），具体取决于数据库配置。

---

## **二、UNION 操作符**
1. **合并查询结果**  
   ```sql
   SELECT ... UNION SELECT ...
   ```
   - 示例：联合两个学期的课程 ID。
   - **易错点**：`UNION` 自动去重，`UNION ALL` 保留重复记录。
   - **相似点**：`UNION` 要求列数/类型一致，而 `JOIN` 用于横向连接表。

---

### **三、DDL（数据定义语言）**
1. **创建表**  
   ```sql
   CREATE TABLE 表名 (
     列名 数据类型 [约束],
     PRIMARY KEY(列名),
     FOREIGN KEY(列名) REFERENCES 被引用表(列名)
   );
   ```
   - 示例：`create table sc(...)`。
   - **易错点**：外键需引用已存在的表和列，否则报错。

2. **修改表结构**  
   ```sql
   ALTER TABLE 表名 ADD 列名 数据类型 [约束];
   ```
   - 示例：`alter table student add sex varchar(50) not null;`
   - **易错点**：添加非空列时，若表中已有数据，需指定默认值或分步操作。

---

## **四、约束**
1. **主键约束**  
   ```sql
   PRIMARY KEY(列名)
   ```
   - 唯一且非空，如 `primary key(stuid)`。

2. **外键约束**  
   ```sql
   FOREIGN KEY(列名) REFERENCES 目标表(目标列)
   ```
   - 确保引用完整性，如 `foreign key(cid) references course(cid)`。

3. **非空约束**  
   ```sql
   NOT NULL
   ```
   - 强制列不允许空值，如 `sex varchar(50) not null`。

---

## **五、数据库管理**
1. **数据库操作**  
   ```sql
   CREATE DATABASE 数据库名;
   DROP DATABASE 数据库名;
   USE 数据库名;
   ```
   - 示例：`create database test;`

2. **查看元数据**  
   ```sql
   SHOW TABLES;      -- 查看所有表
   DESC 表名;        -- 查看表结构
   ```

---

## **六、易混淆知识点**
1. **`UNION` vs `JOIN`**  
   - `UNION` 纵向合并结果集，`JOIN` 横向关联表。

2. **`PRIMARY KEY` vs `UNIQUE`**  
   - 主键隐含非空，唯一约束允许空值（除非显式添加 `NOT NULL`）。

3. **`WHERE` vs `HAVING`**  
   - `WHERE` 过滤行，`HAVING` 过滤分组（通常与 `GROUP BY` 配合）。

---

## **七、总结**
- **重点注意**：  
  1. 字符串条件值的引号与拼写正确性。  
  2. `UNION` 去重特性与 `JOIN` 的关联逻辑。  
  3. 外键约束的引用完整性。  
  4. 添加非空列的潜在风险。  
  5. 区分大小写敏感的场景（如条件值、表名/列名）。  


# 第三周

## 1. 修改表结构  

### 语法：  

```sql  
ALTER TABLE 表名 ADD 列名 数据类型 [约束];  
```

### 示例：  
```sql  
ALTER TABLE student ADD sex VARCHAR(50) NOT NULL;  -- 添加非空列  
ALTER TABLE student ADD address VARCHAR(50);       -- 添加可为空的列  
```
**注意**：  
- 添加非空列时，若表中已有数据，需指定默认值或分步操作（先添加列再更新数据）。  
- 数据类型需明确（如 `VARCHAR(50)`、`INT`）。  

---

## **2. 基础查询**  
### 语法：  
```sql  
SELECT 列名1, 列名2 FROM 表名 [WHERE 条件];  
```

### 示例：  
```sql  
SELECT name, salary FROM instructor WHERE salary > 80000;  
SELECT dept_name, salary/12 AS month_salary FROM instructor;  -- 计算月薪  
```
  
>`WHERE` 条件中字符串需用单引号（如 `dept_name='Comp. Sci.'`）
>字段名歧义时需指定表名（如 `instructor.dept_name`）

  


**计算字段值**

-   `SELECT column_name, expression AS alias_name FROM table_name;` 计算并为新列命名。
    
-   **示例:** 计算教师的月薪:
    
    ```
    SELECT dept_name, salary/12 AS month_salary FROM instructor;
    ```
    
    >其中 `salary/12` 计算月薪，`AS month_salary` 为其取别名,取别名（**alias**）不会存储在数据库中，而只是用于当前查询的结果集


## **3. 范围与模糊查询**  
### 语法：  
```sql  
WHERE 列名 BETWEEN 值1 AND 值2;          -- 闭区间范围  
WHERE 列名 LIKE '模式';                  -- 模糊匹配  
```
### 示例：  
```sql  
WHERE salary BETWEEN 60000 AND 80000;     -- 包含 60000 和 80000  
WHERE name LIKE '%ing%';                 -- 包含 "ing" 的名字  
WHERE name LIKE '_u%';                   -- 第二个字符为 "u" 的名字  
```

>`NOT BETWEEN` 表示范围外  
>`LIKE` 区分大小写（取决于数据库配置）

---

## **4. 多表连接**  
### 语法：  
```sql  
SELECT 表1.列名, 表2.列名  
FROM 表1, 表2  
WHERE 表1.关联字段 = 表2.关联字段 [AND 条件];  
```
### 示例：  
```sql  
SELECT name, course_id  
FROM instructor, teaches  
WHERE instructor.id = teaches.id;  -- 隐式连接  

SELECT name, course.course_id, title  
FROM instructor, teaches, course  
WHERE instructor.id = teaches.id  
  AND teaches.course_id = course.course_id  
  AND instructor.dept_name = 'Comp. Sci.';  
```
>多表字段名冲突需明确表名（如 `course.course_id`）  
>推荐使用显式 `JOIN` 语法提高可读性。 

#### WHERE instructor.id = teaches.id

这部分的作用是 **连接 `instructor` 和 `teaches` 表**，即匹配教师和他们教授的课程。

-   `instructor.id` 是 `instructor` 表中的教师编号。
-   `teaches.id` 是 `teaches` 表中表示授课教师的编号。
-   这个条件确保 `teaches` 表的记录和 `instructor` 表中的教师对应起来

#### AND teaches.course_id = course.course_id

这个条件连接 `teaches` 和 `course` 表，确保授课记录中的课程编号和 `course` 表中的课程编号匹配，即找出每位教师所教授的课程的详细信息

####  AND instructor.dept_name = 'Comp. Sci.'

只筛选 `instructor` 表中 `dept_name` 为 `'Comp. Sci.'`（计算机科学系）的教师
>如果不加这些条件，查询结果会出现 **笛卡尔积（Cartesian Product）**，即 `instructor` 和 `teaches`、`course` 之间的所有组合，数据会混乱

### 简单的例子
假设我们有以下三张表

**教师表 (instructor)**
| id  | name    | dept_name  |
|-----|---------|------------|
| 101 | Alice   | Comp. Sci. |
| 102 | Bob     | Comp. Sci. |
| 103 | Charlie | Math       |

**授课表 (teaches)**
| id  | course_id |
|-----|-----------|
| 101 | CS101     |
| 101 | CS102     |
| 102 | CS103     |
| 103 | MATH101   |

**课程表 (course)**
| course_id | title             |
|-----------|-------------------|
| CS101     | Data Structures   |
| CS102     | Algorithms        |
| CS103     | Operating Systems |
| MATH101   | Calculus          |
>在 `teaches` 表中，`id` 不是主键，而 `id + course_id` 的组合才可能是主键。让我们详细分析这个问题
#### 逐步执行：

1.  **`instructor.id = teaches.id`**
    
    -   这一步连接 `instructor` 和 `teaches`，得到：
        
        
        | id  | name    | dept_name  | id  | course_id |
        |-----|--------|-----------|-----|----------|
        | 101 | Alice  | Comp. Sci. |101 | CS101    | 
        | 101 | Alice  | Comp. Sci. | 101 | CS102    | 
        | 102 | Bob    | Comp. Sci. | 102 | CS103    |
        | 103 | Charlie| Math       | 103 | MATH101  |  
        >Charlie 还在
        
2.  **`AND instructor.dept_name = 'Comp. Sci.'`**
    
    -   这一步过滤掉 **不属于 `'Comp. Sci.'` 的教师**，即 **`103 (Charlie)` 被排除**，剩下：

        | id  | name  | dept_name  | id  | course_id |
        |-----|------|-----------|-----|----------|
        | 101 | Alice | Comp. Sci. | 101 | CS101    |
        | 101 | Alice | Comp. Sci. | 101 | CS102    |
        | 102 | Bob   | Comp. Sci. | 102 | CS103    |
        
3.  **`AND teaches.course_id = course.course_id`**
    
    -   连接 `course` 表，获取课程名称：
        
        | name  | course_id | title             |
        |-------|----------|------------------|
        | Alice | CS101    | Data Structures  |
        | Alice | CS102    | Algorithms       | 
        | Bob   | CS103    | Operating Systems |


## **5. 聚合与分组**  
### 语法：  
```sql  
SELECT 聚合函数(列名) FROM 表名 [GROUP BY 列名] [HAVING 条件];  
```
### 示例：  
```sql  
SELECT SUM(salary) AS total_salary, AVG(salary) AS avg_salary FROM instructor;  
SELECT dept_name, COUNT(*) FROM instructor GROUP BY dept_name;  
SELECT dept_name, AVG(salary) FROM instructor GROUP BY dept_name HAVING AVG(salary) > 60000;  
```
>`COUNT(*)` 统计所有行，`COUNT(列名)` 忽略 NULL
>`HAVING` 必须与 `GROUP BY` 配合使用


#### 第一条 SQL 语句

```sql
SELECT SUM(salary) AS total_salary, AVG(salary) AS avg_salary FROM instructor;
```

##### 作用：
计算 `instructor` 表中所有教师的工资总和 (`SUM(salary)`) 和平均工资 (`AVG(salary)`)。

##### 执行流程：
1. **SUM(salary)**: 计算 `salary` 列所有教师工资的总和。
2. **AVG(salary)**: 计算 `salary` 列所有教师工资的平均值。
3. **AS total_salary 和 AS avg_salary**: 仅用于将结果列重命名，方便阅读。

##### 示例数据（`instructor` 表）

| id  | name    | dept_name  | salary |
|-----|---------|------------|--------|
| 101 | Alice   | Comp. Sci. | 70000  |
| 102 | Bob     | Comp. Sci. | 60000  |
| 103 | Charlie | Math       | 80000  |

##### 计算：
- **SUM(salary)** = 70000 + 60000 + 80000 = **210000**
- **AVG(salary)** = (70000 + 60000 + 80000) / 3 = **70000**

##### 返回结果

| total_salary | avg_salary |
|--------------|------------|
| 210000       | 70000      |


---

#### 第二条 SQL 语句

```sql
SELECT dept_name, COUNT(*) FROM instructor GROUP BY dept_name;
```

##### 作用：
统计 `instructor` 表中 **每个系** 的教师人数。

##### 执行流程：
1. **GROUP BY dept_name**: 按 `dept_name`（系名）分组，每个组包含该系的所有教师。
2. **COUNT(*)**: 计算每个 `dept_name` 组内的行数（即该系的教师总数）。

##### 示例数据

| id  | name    | dept_name  | salary |
|-----|---------|------------|--------|
| 101 | Alice   | Comp. Sci. | 70000  |
| 102 | Bob     | Comp. Sci. | 60000  |
| 103 | Charlie | Math       | 80000  |
| 104 | David   | Math       | 65000  |

##### 按照 `dept_name` 分组：
- **Comp. Sci. 组**：Alice, Bob → 2人
- **Math 组**：Charlie, David → 2人

##### 返回结果

| dept_name  | COUNT(*) |
|------------|----------|
| Comp. Sci. | 2        |
| Math       | 2        |

---

#### 第三条 SQL 语句

```sql
SELECT dept_name, AVG(salary) 
FROM instructor 
GROUP BY dept_name 
HAVING AVG(salary) > 60000;
```

##### 作用：
统计 **平均工资高于 60000** 的系，并返回系名和该系的平均工资。

##### 执行流程：
1. **GROUP BY dept_name**: 按 `dept_name`（系名）分组，每个组包含该系的所有教师。
2. **AVG(salary)**: 计算每个 `dept_name` 组的平均工资。
3. **HAVING AVG(salary) > 60000**: 过滤掉平均工资 ≤ 60000 的系，只保留 `AVG(salary) > 60000` 的系。

##### 示例数据

| id  | name    | dept_name  | salary |
|-----|---------|------------|--------|
| 101 | Alice   | Comp. Sci. | 70000  |
| 102 | Bob     | Comp. Sci. | 60000  |
| 103 | Charlie | Math       | 80000  |
| 104 | David   | Math       | 65000  |

##### 计算 `AVG(salary)`：
- **Comp. Sci.**: (70000 + 60000) / 2 = **65000**
- **Math**: (80000 + 65000) / 2 = **72500**

##### **HAVING** 过滤：
- **Comp. Sci.**: 65000 ✅（保留）
- **Math**: 72500 ✅（保留）

##### 返回结果

| dept_name  | AVG(salary) |
|------------|-------------|
| Comp. Sci. | 65000       |
| Math       | 72500       |


## **6. 子查询**  
### 语法：  
```sql  
WHERE 列名 比较运算符 (子查询);  
WHERE EXISTS (子查询);  
```
### 示例：  
```sql  
-- 工资高于 Comp. Sci. 部门最低工资的教师  
SELECT name FROM instructor  
WHERE salary > (SELECT MIN(salary) FROM instructor WHERE dept_name = 'Comp. Sci.');  
```
```sql
-- 选修了所有 Biology 课程的学生  
SELECT S.ID, S.name  
FROM student AS S  
WHERE NOT EXISTS (  
  SELECT * FROM course AS C  
  WHERE C.dept_name = 'Biology'  
    AND NOT EXISTS (  
      SELECT * FROM takes  
      WHERE takes.id = S.id AND takes.course_id = C.course_id  
    )  
);  
```
>`SOME` / `ANY` 等价于比较子查询的最小值
>`ALL` 等价于比较子查询的最大值。  



#### 第一条 SQL 语句

```sql
-- 工资高于 Comp. Sci. 部门最低工资的教师
SELECT name 
FROM instructor  
WHERE salary > (SELECT MIN(salary) FROM instructor WHERE dept_name = 'Comp. Sci.');
```

##### 作用：
查询 `instructor` 表中工资 **高于 Comp. Sci. 部门最低工资** 的所有教师的名字。

##### 执行流程

1. **子查询**：
   ```sql
   (SELECT MIN(salary) FROM instructor WHERE dept_name = 'Comp. Sci.')
   ```
   - 先在 `instructor` 表中过滤出 `Comp. Sci.` 部门的教师。
   - 然后计算该部门的 **最低工资**（`MIN(salary)`）。

2. **外查询**：
   ```sql
   SELECT name FROM instructor WHERE salary > (子查询结果);
   ```
   - 在 `instructor` 表中查找所有 **工资高于子查询计算出的最低工资** 的教师，并返回他们的 `name`。

##### 示例数据

| id  | name    | dept_name  | salary |
|-----|---------|------------|--------|
| 101 | Alice   | Comp. Sci. | 70000  |
| 102 | Bob     | Comp. Sci. | 60000  |
| 103 | Charlie | Math       | 80000  |
| 104 | David   | Physics    | 75000  |

##### 子查询计算
- **Comp. Sci. 部门**有：
  - Alice（70000）
  - Bob（60000）
- **MIN(salary)** = 60000（Bob 的工资）

##### 外查询筛选
找出 `salary > 60000` 的教师：
- Alice (70000) ✅（符合）
- Charlie (80000) ✅（符合）
- David (75000) ✅（符合）
- Bob (60000) ❌（不符合）

##### 返回结果

| name    |
|---------|
| Alice   |
| Charlie |
| David   |

---



#### 第二条 SQL 语句

```sql
-- 选修了所有 Biology 课程的学生  
SELECT S.ID, S.name  
FROM student AS S  
WHERE NOT EXISTS (  
  SELECT * FROM course AS C  
  WHERE C.dept_name = 'Biology'  
    AND NOT EXISTS (  
      SELECT * FROM takes  
      WHERE takes.id = S.id AND takes.course_id = C.course_id  
    )  
);
```

##### 作用：
查询 **选修了 Biology 系所有课程** 的学生。

##### 执行流程

1. **子查询 1（course 表）**：
   ```sql
   SELECT * FROM course AS C  
   WHERE C.dept_name = 'Biology'
   ```
   - 选出所有 **Biology 系** 的课程。

2. **子查询 2（NOT EXISTS 检查）**：
   ```sql
   NOT EXISTS (  
     SELECT * FROM takes  
     WHERE takes.id = S.id AND takes.course_id = C.course_id  
   )
   ```
   - 这里的 `NOT EXISTS` 作用是：
     - 检查 `student` 是否 **没有** 选修 Biology 课程 `C.course_id`。
     - 若 `NOT EXISTS` 为 `TRUE`，表示该学生没有选修某个 Biology 课程，则该学生被排除。

3. **外层 NOT EXISTS**：
   ```sql
   WHERE NOT EXISTS ( ... )
   ```
   - 只有当 `student` 选修了 **所有 Biology 课程** 时，`NOT EXISTS` 结果才是 `FALSE`，该 `student` 才会被保留。

##### 示例数据

**student 表**

| ID  | name  |
|-----|-------|
| 1   | Tom   |
| 2   | Jerry |
| 3   | Alice |

**course 表（Biology 课程）**

| course_id | dept_name |
|-----------|-----------|
| BIO101    | Biology   |
| BIO102    | Biology   |

**takes 表（选课情况）**

| id  | course_id |
|-----|-----------|
| 1   | BIO101    |
| 1   | BIO102    |
| 2   | BIO101    |
| 3   | BIO101    |
| 3   | BIO102    |

##### 执行过程

1. **WHERE C.dept_name = 'Biology'**：
   - Biology 课程：`BIO101`, `BIO102`。

2. **NOT EXISTS (SELECT * FROM takes WHERE takes.id = S.id AND takes.course_id = C.course_id)**：
   - 对于每个学生检查是否有 Biology 课程未选修：
     - **Tom（ID = 1）**：
       - 选了 `BIO101` 和 `BIO102` ✅（没有 Biology 课程遗漏）
       - `NOT EXISTS = FALSE`（不遗漏）
       - `NOT EXISTS(...) = FALSE`，所以 Tom 被 **保留**。
     - **Jerry（ID = 2）**：
       - 选了 `BIO101`，但没选 `BIO102` ❌
       - `NOT EXISTS = TRUE`（有遗漏）
       - `NOT EXISTS(...) = TRUE`，所以 Jerry 被 **排除**。
     - **Alice（ID = 3）**：
       - 选了 `BIO101` 和 `BIO102` ✅（没有 Biology 课程遗漏）
       - `NOT EXISTS = FALSE`（不遗漏）
       - `NOT EXISTS(...) = FALSE`，所以 Alice 被 **保留**。

##### 最终结果

| ID  | name  |
|-----|-------|
| 1   | Tom   |
| 3   | Alice |



## **7. 数据插入与外键约束**  
### 语法：  
```sql  
INSERT INTO 表名 (列1, 列2) VALUES (值1, 值2);  
```
### 示例：  
```sql  
-- 正确插入（需先满足外键引用）  
INSERT INTO section VALUES ('BIO-399', 1, 'FALL', '2019', NULL, NULL, NULL);  
INSERT INTO takes VALUES ('98988', 'BIO-399', 1, 'FALL', '2019', NULL);  

-- 错误示例（外键冲突）  
INSERT INTO takes (id, course_id) VALUES ('98988', 'BIO-399');  -- 缺少被引用的 section 数据  
```
>外键字段必须引用已存在的值
>插入顺序应为父表 → 子表

>在数据库中，**外键（Foreign Key）** 是用来确保数据的 **完整性和一致性** 的。它规定了某个表中的字段 **必须在另一个表中存在**，否则插入或更新数据时就会发生**外键冲突**

>一句话，先有鸡后有蛋，父亲才能生儿子，儿子不能生父亲

假设有两张表：

-   **`department`**（部门表）：
    
```sql
CREATE TABLE department (
	dept_name VARCHAR(50) PRIMARY KEY,
	budget DECIMAL(10,2) );
```
-   **`instructor`**（教师表），其中 `dept_name` 是外键，引用 `department` 表：
    
```sql
CREATE TABLE instructor (
	id INT PRIMARY KEY,
	name VARCHAR(50),
	dept_name VARCHAR(50),
	FOREIGN KEY (dept_name) REFERENCES department(dept_name) );`
```

✔ **正确插入**

```sql
INSERT INTO department VALUES ('Comp. Sci.', 100000);  -- 先插入部门
INSERT INTO instructor VALUES (101, 'Alice', 'Comp. Sci.');  -- 再插入教师
```
🔴 **错误插入**

```sql
INSERT INTO instructor VALUES (102, 'Bob', 'Math');   -- 错误！'Math' 部门不存在于 department 表中，导致外键冲突
```

## **总结**  
- **通用规则**：  
  1. 字段名歧义时需指定表名（如 `表名.列名`）。  
  2. 字符串用单引号，数值无需引号。  
  3. 外键操作需确保引用完整性。  
- **易错点**：  
  - `WHERE dept_name = NULL` 应改为 `WHERE dept_name IS NULL`。  
  - 多表连接未指定条件会导致笛卡尔积。  
- **优化建议**：  
  - 使用显式 `JOIN` 代替隐式连接。  
  - 避免 `SELECT *`，明确指定字段。  
