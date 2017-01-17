---
layout: post
title:  "MySQL和SQL入门"
categories: MySQL SQL
tags:  入门
---

* content
{:toc}

如果你正打算挑选一种数据可产品，那么 MySQL 绝对是理想的候选。MySQL 就是一种免费的数据库系统，它是一种客户/服务器模式的关系数据库管理系统。





## MySQL 的用途

当需要组织和管理的信息很多或者信息本身很复杂时，使用数据库系统则会让数据的处理变得轻而易举。

## 数据库的组织结构
**MySQL** 属于关系数据库管理系统（Relation Database Management System，**RDBMS**）。
**数据库**就是一个用来存放信息的仓库，它们构造简单，遵守一定的规则：

- 数据库里的数据集合都存放在数据表（**table**）里。
- 数据表由数据行（**row**）和数据列（**column**）构成。
- 一个数据行就是数据表里的一条记录（**record**）。
- 记录可以包含多个信息项，数据表里的每一个数据列都对应一个信息项。

**管理系统**指的是用来对数据进行插入、检索、修改、删除等操作的软件。
**关系**表示 RDBMS 是 DBMS 中的一种，它能方便地抽取出数据表里的数据并把它们与其他相关数据表的信息结合起来。

## MySQL 的体系结构
MySQl 采用的是客户/服务器体系结构。

- **MySQL 服务器程序**，指的是 mysqld 程序，它运行在存放着你数据库的机器上，它负责在网络上监听并处理来自客户的服务请求。
- **MySQL 客户程序**，它们负责连接到数据库服务器，并通过向服务器发出查询命令告知它们需要哪些信息。

    + **mysql** 是最常用的客户程序，它是一个交互式的客户程序，你通过它发出查询命令并查看结果。
    + **mysqldump** 用于数据库管理的客户程序，把数据表的内容导出到一个文件里。
    + **mysqladmin** 用于数据库管理的客户程序，检查数据库服务器的工作状态和执行一些数据库管理方面的任务。

MySQL 的“客户/服务器”体系结构有以下一些好处：

- **并发控制**（concurrency control) 由服务器提供，因而不会出现两个用户同时修改同一条记录的现象。
- 可以在任意地点运行 MySQL 客户程序，这个客户程序能够通过网络找到服务器。

## MySQL

### 如何建立和断开与服务器的连接
```
$ mysql -h host_name -p -u user_name               # 连接服务器
mysql>quit                                         # 断开连接
```

### 执行 SQL 语句
```
mysql>SELECT NOW();                       # 查看系统的当前日期和时间
mysql>SELECT NOW(),USER(),VERSION()\G     # \G 竖直排列显示结果，每行一个值
mysql>SELECT NOW(),VERSION,\c             # \c 取消已输入的命令
```

### 创建数据库
```
mysql>CREATE DATABASE sampdb;         # 创建 sampdb 数据库
mysql>SELECT DATABASE;                # 查看当前默认数据库
mysql>SHOW DATABASES;                 # 列出当前连接的服务器上的数据库
mysql>USE sampdb;                     # 设置当前默认数据库
```

### 创建数据表
```
mysql>CREATE TABLE tbl_name (column_specs);        # 创建数据表语句的格式
mysql>CREATE TABLE president                       # 创建历史研究会的总统信息表
      (
        last_name VARCHAR(15) NOT NULL,          
        # VARCHAR(n) 这个数据列存放着长度可变的字符（串）值，最多有 n 个字符      
        first_name VARCHAR(15) NOT NULL,
        suffix VARCHAR(5) NULL,
        city VARCHAR(20) NOT NULL,
        state VARCHAR(2) NOT NULL,
        birth DATE NOT NULL,                    
        # DATE 保存日期值，格式"CCYY-MM-DD"
        death DATE NULL              
      );
      CREATE TABLE member                          # 创建历史研究会的会员数据表
      (
        member_id INT UNSIGNED NOT NULL AUTO_INCREMENT, 
        # INT 表示这个数据列将用来保存整数值（没有小数部分的数字）
        # UNSIGNED 不允许出现负数
        # NOT NULL 必须填有数据，不得为空
        # AUTO_INCREMENT 表示数据列存放的是序列编号且必须拥有某种形式的唯一化索引
        # PRIMARY KEY 需求该数据列的各个值都必须是唯一的且 NOT NULL              
        last_name VARCHAR(20) NOT NULL,
        first_name VARCHAR(20) NOT NULL,
        suffix VARCHAR(5) NULL,
        expiration DATE NULL,                     
        email VARCHAR(100) NULL              
      );
```

### 查看表结构
```
mysql>DESCRIBE president;                           # 查看数据表结构
mysql>DESCRIBE president '%name';                   # 查看指定列的有关信息
mysql>SHOW COLUMNS FROM president LIKE '%name';     # 同上
mysql>SHOW TABLES;                                  # 列出当前默认数据库里的数据表
```

### 如何添加新的数据行
```
# 添加新的数据行
mysql>INSERT INTO tbl_name (col_name1,col_name2,...) VALUES (value1,value2,...);
# 从文件中读取来添加新行
mysql>source insert_president.sql;
# 文件里的记录项不是以 INSERT 语句而是以纯数据值的形式存放，用 LOAD DATA 来加载
# LOCAL 如省略表示数据文件是保存在服务器主机上，你必须拥有相应的访问权限才能把文件里的数据加载到数据表
mysql>LOAD DATA LOCAL INFILE 'member.txt' INTO TABLE member;
```

### 检索信息
```
mysql>SELECT * FROM president;                           # 显示整个数据表的内容
mysql>SELECT name,sex FROM student;                      # 显示指定的数据列
mysql>SELECT * FROM score WHERE score > 95;              # 指定检索条件
# WHERE 字句的表达式允许使用算术运算符（+ - * / %）比较运算符（< <= = <=> <>或!= >= >）和逻辑运算符（AND OR XOR NOT）
mysql>SELECT last_name,first_name FROM president WHERE last_name = 'ROOSEVELT';
mysql>SELECT last_name,first_name FROM president WHERE state = 'VA' OR state = 'MA';
# NULL是一个很特殊的值，它的含义是”无数据“或”未知数据“，所以不能用它与”有数据“的值进行比较，除非用 IS NULL 或 IS NOT NULL 判断
mysql>SELECT last_name,first_name FROM president WHERE death IS NULL;
```

### 如何对查询结果进行排序
```
# 查询总统的姓名根据姓降序排列,ASC 升序排列（ORDER BY子句的默认排序方式），DESC 降序排列
mysql>SELECT last_name,first_name FROM president ORDER BY last_name DESC;
# 按逝世日期的降序对总统的姓名排序，但健在（即逝世日期为 NULL）总统的姓名出现在查询结果的开头
mysql>SELECT last_name,first_name,death FROM president ORDER BY IF(death IS NULL,0,1),death DESC;
```

### 如何限制查询结果中的数据行个数
```
# 查询出生日期最早的前5个总统
mysql>SELECT last_name,first_name,birth FROM president ORDER BY birth LIMIT 5;
# 查询出生日期在前10个总统之后的5个总统
mysql>SELECT last_name,first_name,birth FROM president ORDER BY birth LIMIT 10,5;
```

### 如何对输出列进行求值和命名
```
mysql>SELECT CONCAT(first_name,' ',last_name) AS Name,CONCAT(city,',',state)
      AS 'place of birth' FROM president;
```

### 与日期有关的问题
```
# 日期相关函数 TO_DAYS(CURDATE())、MONTHNAME(birth)、DAYOFMONTH(birth)等
mysql>SELECT last_name,first_name,birth,TIMESTAMPDIFF(YEAR,birth,death) AS age
      FROM president WHERE MONTH(birth) = 3;
mysql>SELECT last_name,first_name,death FROM president WHERE 
      death >= '1970-1-1' AND death < DATE_ADD('1970-1-1',INTERVAL 10 YEAR);
# 查询资格已经失效和需要在60天以内续交会费的会员
mysql>SELECT last_name,first_name,expiration FROM member 
      WHERE (TO_DAYS(expiration) - TO_DAYS(CURDATE))) < 60;
```

### 模式匹配
```
# 下划线字符"_"只能匹配一个字符，百分号字符"%"能匹配任何一个字符序列（包括空序列在内）
mysql>SELECT last_name,first_name,death FROM president WHERE last_name LIKE 'W%';
```

### 如何设置和使用 SQL 变量
```
# 变量的命名语法是”@变量名“，赋值语法是在 SELECT 语句里使用一个”@变量名:=值“的语法形式
mysql>SELECT @birth:=birth FROM president WHERE last_name = 'Jackson' 
      AND first_name = 'Andrew';
mysql>SELECT last_name,first_name,birth FROM president 
      WHERE birth < @birth ORDER BY birth;
```

### 如何生成统计信息
```
# DINSTINCT 清除重复出现的数据行
mysql>SELECT DINSTINCT state FROM president ORDER BY state;    
# COUNT() 函数用于计数,COUNt(*) 统计被选中的数据列，COUNT(数据列名称) 统计非 NULL 值的个数
mysql>SELECT COUNT(*),COUNT(email),COUNT(expiration) FROM member;
# 使用 GROUP BY 子句，可以把数据列的不同值分别出现过多少次的情况统计出来
# 分别统计男女学生的个数
mysql>SELECT sex,COUNT(*) FROM student GROUP BY sex;
# 新增加的 WITH ROLLUP 子句将对两种性别的学生人数进行汇兑并生成一个输出行
mysql>SELECT sex,COUNT(*) FROM student GROUP BY sex WITH ROLLUP;
# 分别统计不同月份出生的总统的人数
mysql>SELECT MONTH(birth) AS Month,MONTHNAME(birth) AS Name,COUNT(*) AS Count
      FROM president GROUP BY Name ORDER BY Month;
# 查询有哪些州有两位或两位以上的总统出生
mysql>SELECT state,COUNT(*) AS Count FROM GROUP BY state 
      HAVING count > 1 ORDER BY Count DESC;
# 查询在同一个州出生的总统逝世时的平均年龄
mysql>SELECT state as State,AVG(TIMESTAMPDIFF(YEAR,birth,death)) AS Age 
      FROM president WHERE death IS NOT NULL GROUP BY State ORDER BY Age;
```

### HAVING 与 WHERE 异同点
- HAVING 与 WHERE 功能、用法相同，都是用来设定查询条件，但执行时机不同。
- WHERE 在开始时执行检测数据，对原数据进行过滤,HAVING 对筛选出的结果再次进行过滤。
- HAVING 字段必须是查询出来的，WHERE 字段必须是数据表存在的。
- WHERE 不可以使用字段的别名，HAVING 可以。因为执行 WHERE 代码时，可能尚未确定列值。
- WHERE 不可以使用合计函数，一般需用合计函数才会用 HAVING。

### 创建考试记分相关数据表
```
mysql>CREATE TABLE student                         # 创建学生数据表
      (     
        name VARCHAR(20) NOT NULL,
        sex ENUM('F','M') NOT NULL,
        student_id INT UNSIGNED NOT NULL AUTO_INCREMENT,
        PRIMARY KEY (student_id)              
      ) ENGINE = InnoDB;
mysql>CREATE TABLE grade_event                     # 创建考试事件数据表
      (
        name VARCHAR(20) NOT NULL,
        date DATE NOT NULL,
        category ENUM('T','Q') NOT NULL,         # T 考试 Q 测试
        event_id INT UNSIGNED NOT NULL AUTO_INCREMENT,
        PRIMARY KEY (event_id)               
      ) ENGINE = InnoDB;
mysql>CREATE TABLE score                           # 创建成绩数据表
      (
        student_id INT UNSIGNED NOT NULL,
        event_id INT UNSIGNED NOT NULL,
        score INT NOT NULL,
        event_id INT UNSIGNED NOT NULL AUTO_INCREMENT,
        PRIMARY KEY (event_id,student_id),
        INDEX (student_id),
        # FOREIGN KEY 确保考试成绩里不会有在 grade_event 或 student 数据表里并不存在的虚假 ID 值
        FOREIGN KEY (event_id) REFERENCES grade_event (evetn_id),
        # 对于出现在 FOREIGN KEY 定义里的每个数据列，要么本身有一个索引或者是某个多数据列索引里第一个被列出的数据列
        FOREIGN KEY (student_id) REFERENCES student (student_id)             
      ) ENGINE = InnoDB;
mysql>CREATE TABLE absence                          # 创建缺勤数据表
      (
        student_id INT UNSIGNED NOT NULL,
        date DATE NOT NULL,
        PRIMARY KEY (student_id,date),
        FOREIGN KEY (student_id) REFERENCES student (student_id)                
      ) ENGINE = InnoDB;
```


### 如何从多个数据表里检索信息
```
# 查询给定日期的考试或测试分数，显示学号、考试分数、日期、考试事件
# 要先查出给定日期 2016-09-23 的 grade_event 行，再利用此行里的
# event_id 把 score 数据表里拥有同一 event_id 的考试或测试分数查出来
mysql>SELECT student_id,date,score,category FROM grade_event INNER JOIN score
      ON grade_event.event_id = score.event_id WHERE date = '2016-09-23';
# 查询给定日期的考试或测试分数，显示学生姓名、考试分数、日期、考试事件
# 利用上述查询 score 数据表 的 student_id 关联 student 数据表
mysql>SELECT student.name,grade_event.date,score.score,grade_event.category 
      FROM grade_event 
      INNER JOIN score 
      INNER JOIN student 
      ON grade_event.event_id = score.event_id 
      AND score.student_id = student.student_id 
      WHERE grade_event.date = '2016-09-23';
# 统计学生们的考试缺勤情况
mysql>SELECT student.student_id,student.name,COUNT(absence.date) AS absences
      FROM student INNER JOIN absence 
      ON student.student_id = absence.student_id
      GROUP BY student.student_id;
# 考试日期与考生性别的每一种组合相对应的考生人数和平均分统计
mysql>SELECT grade_event.date,student.sex,COUNT(score.score) AS Count,
      AVG(score.score) AS average
      FROM grade_event INNER JOIN score INNER JOIN student
      ON grade_event.event_id = score.event_id 
      AND score.student_id = student.student_id
      GROUP BY grade_event.date,student.sex;
# 联结操作可作用于相同的数据表，查看同一天出生的总统
mysql>SELECT p1.last_name,p1.first_name,pi.birth 
      FROM president AS p1 
      INNER JOIN president AS p2
      WHERE MONTH(p1.birth) = MONTH(p2.birth)
      AND DAYOFMONTH(p1.birth) = DAYOFMONTH(p2.birth)
      AND (p1.last_name <> p2.last_name OR p1.first_name <> p2.first_name)
      ORDER BY p1.last_name;
# 进行多表检查的另一种办法是子查询，也就是把一条 SELECT 语句套在另一条里
# 子查询没有缺勤的学生
mysql>SELECT * FROM student WHERE student_id 
      NOT IN (SELECT student_id FROM absence);
# 子查询哪些总统出生在 Andrew Jackson 之前
mysql>SELECT last_name,first_name,birth FROM president WHERE birth < 
      (SELECT birth FROM president 
      WHERE last_name = 'Jackson' AND first_name = 'Andrew');
```

### 如何删除或更新现有的数据行
```
mysql>DELETE FROM tbl_name WHERE which rows to delete;  # 删除数据行语句格式
mysql>DELETE FROM tbl_name;                             # 删除数据表里的全部数据行
mysql>DELETE FROM president WHERE state = 'OH';         # 删除满足条件部分的数据记录
# 更新数据行语句格式
mysql>UPDATE tbl_name SET which columns to change WHERE which rows to update; 
mysql>UPDATE member SET email='abc@163.com',suffix='jr' WHERE last_name='YORK';
```

### MYSQL 账户管理
```
# 创建新账户并为它设置密码（可选）语句格式
mysql>CREATE USER account [IDENTIFIED BY 'password'];
# host_name 为 % 表示任意一台主机
mysql>CREATE USER 'user_name'@'host_name' INDENTIFIED BY 'secret';
# 删除一个现有账户及该账户相关联的全部权限的语句格式
mysql>DROP USER account；
# 改变现有账户名字的语句格式
mysql>RENAME USER from_account TO to_account;
# 授予账户权限的语句格式
mysql>GRANT privileges (columns) ON what TO 
            account [IDENTIFIED BY 'password']
            [REQUIRE encryption requirements]
            [WITH grant or resource management options];
# privileges 授予账户的权限，比如说 SELECT 权限将允许用户发出 SELECT 语句
# SHUTDOWN 权限将允许用户关停服务器，一次可授予多项权限，用逗号隔开
# what 权限的级别，分为全局级、数据库级、数据表级、数据列级或存储例程级
# password 账户的口令（可选），如果账户已存在并有一个口令，就不必写出这个子句
# REQUIRE 和 WITH 子句都是可选的，REQUIRE 是对必须经由 SSL 进行安全连接的账户进行设置
# WITH 子句用来授予 GRANT OPTION 权限（把自己的权限授予给他人）还可以用来设置资源管理选项，限制用户的资源占用量
mysql>GRANT ALL ON sampdb.* TO 'sampadm'@'loaclhost';
# 查看账户的权限
mysql>SHOW GRANT FOR 'sampadm'@'loaclhost';
# 查看自己的quanx
mysql>SHOW GRANTS；
# 改变口令
mysql>SET PASSWORD FOR 'sampadm'@'loaclhost' = PASSWORD('sillicon');
```


