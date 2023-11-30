---
title: MySQL
date: 2023-11-30 11:25:45
# tags:
---

# MySQL

- [MySQL](#mysql)
  - [MySQL概述](#mysql概述)
    - [1](#1)
  - [SQL](#sql)
    - [1](#1-1)
    - [DDL](#ddl)
    - [DML](#dml)
    - [DQL](#dql)
    - [DCL](#dcl)
  - [函数](#函数)
    - [字符串函数](#字符串函数)
    - [数值函数](#数值函数)
    - [日期函数](#日期函数)
    - [流程函数](#流程函数)
  - [约束](#约束)
    - [1](#1-2)
  - [多表查询](#多表查询)
    - [1](#1-3)
  - [事务](#事务)
    - [1](#1-4)
    - [事务的四大特性](#事务的四大特性)
    - [并发事务问题](#并发事务问题)
    - [事务隔离级别](#事务隔离级别)
  - [存储引擎](#存储引擎)
    - [1](#1-5)
  - [索引](#索引)
    - [1](#1-6)
    - [索引的分类](#索引的分类)
    - [索引语法](#索引语法)
    - [SQL性能分析](#sql性能分析)
    - [索引的使用原则](#索引的使用原则)
    - [索引设计原则](#索引设计原则)
  - [SQL优化](#sql优化)
    - [插入数据](#插入数据)
    - [主键优化](#主键优化)
    - [ORDER BY 优化](#order-by-优化)
    - [GROUP BY 优化](#group-by-优化)
    - [limit 优化](#limit-优化)
    - [count 优化](#count-优化)
    - [update 优化](#update-优化)
  - [视图/存储过程/触发器](#视图存储过程触发器)
    - [视图](#视图)
    - [存储过程](#存储过程)
  - [锁](#锁)
    - [概述](#概述)
    - [全局锁](#全局锁)
    - [表级锁](#表级锁)
    - [行级锁](#行级锁)
  - [InnoDB引擎](#innodb引擎)
    - [逻辑存储结构](#逻辑存储结构)
    - [架构](#架构)
    - [事务原理](#事务原理)
    - [MVCC](#mvcc)
    - [MySQL自增值不连续](#mysql自增值不连续)
  - [日志](#日志)
    - [错误日志](#错误日志)
    - [二进制日志](#二进制日志)
    - [查询日志](#查询日志)
    - [慢查询日志](#慢查询日志)
  - [主从复制](#主从复制)
    - [主从复制](#主从复制-1)
  - [分库分表](#分库分表)
    - [拆分策略](#拆分策略)


## MySQL概述

### 1
区分DB，DBMS，SQL，RDBMS：
- DB是database，数据库，是存储数据的仓库
- DBMS是database management system，是管理数据库的软件，例如：MySQL、SQL Server等
- SQL是structured query language，是用于操纵dbms的一种查询语言
- RDBMS，关系型dbms，建立在关系模型基础上，由多张相互连接的二维表组成

## SQL

### 1
SQL分类
- DDL：Data Definition Language，定义数据库对象（db，table，index）
- DML: Data Manipulation Language，对数据库数据进行增删改
- DQL: Data Query Language，查询数据
- DCL: Data Control Language，创建用户，控制权限

数据类型
- Numeric: TINYINT, SMALLINT, MEDIUMINT, INT, BIGINT / DECIMAL, NUMERIC (exact) / FLOAT, DOUBLE (approximate) / BIT (BIT(M) can store M-bit values, range 1 - 64)
- Date and Time
  - DATE: YYYY-MM-DD
  - TIME: HH:MM:SS
  - YEAR: YYYY
  - DATETIME: YYYY-MM-DD HH:MM:SS
  - TIMESTAMP: YYYY-MM-DD HH:MM:SS 1970-01-01 00:00:01 至 2038-01-19 03:14:07
- String
  - CHAR 0-255 bytes 定长字符串 性能好
  - VARCHAR 0-65535 bytes 变长字符串 性能稍差
  - TEXT 0-65535 bytes 文本数据
  - BLOB 0-65535 bytes 二进制
- Spatial
- JSON
  
### DDL

数据库操作
```SQL
SHOW DATABASES;
CREATE DATABASE DBNAME;
USE DBNAME;
SELECT DATABASE();
DROP DATABASE DBNAME;
```

表操作
```SQL
SHOW TABLES;

CREATE TABLE employee (
    id BIGINT,
    eid VARCHAR(10),
    name VARCHAR(10),
    sex CHAR(1),
    age TINYINT UNSIGNED,
    identification CHAR(18),
    in_time DATE
);

DESC tablename; # 查看表字段及类型
ALTER TABLE tablename MODIFY field INT; # 修改数据类型
ALTER TABLE tablename CHANGE old new INT; # 修改字段名和类型
ALTER TABLE tablename DROP field; # 删除字段
ALTER TABLE oldTableName RENAME TO newTableName; #修改表名
DROP TABLE [IF EXISTS] tablename; # 删除表
TRUNCATE TABLE tablename; # 重新创建表
```

### DML

```SQL
# 给指定字段添加数据
INSERT INTO tablename(col1, col2...) VALUES(val1, val2...);

# 给所有字段添加数据
INSERT INTO tablename VALUES(val1, val2...);

# 批量添加数据
INSERT INTO tablename(col1, col2...) VALUES(val1, val2...), (val1, val2...), (val1, val2...)...;
INSERT INTO tablename VALUES(val1, val2...), (val1, val2...), (val1, val2...)...;

# 字符串和日期类型数据应包含在引号中
```

```SQL
# update 如果没有WHERE，会修改表中全部数据
UPDATE tablename SET col1 = val1, col2 = val2 [WHERE ...];
```

```SQL
# delete 如果没有WHERE，会删除表中全部数据
DELETE FROM tablename [WHERE...];
```

### DQL

```SQL
SELECT col1, col2, ...
FROM tablename
WHERE condition
GROUP BY 分组字段列表
HAVING 分组后条件列表
ORDER BY 排序字段列表
LIMIT 分页参数;
```

```SQL
# 查询多个字段
SELECT col1, col2... FROM tablename;
SELECT * FROM tablename;

# 设置别名
SELECT col1 [AS a], col2 [AS b] FROM tablename;

# 过滤重复字段
SELECT DISTINCT col1, col2... FROM tablename;

# 聚合函数 min max count avg sum, null值不参与聚合函数计算
SELECT 聚合函数(字段列表) FROM tablename;

# WHERE和HAVING的区别：1. WHERE中不能有聚合函数，HAVING可以  2. WHERE是分组前进行过滤，不满足条件，不参与分组；HAVING是分组后对结果进行过滤

# 排序 默认方式是ASC
SELECT * FROM tablename ORDER BY col1 ASC/DESC, col2 ASC/DESC;

# 分页查询 LIMIT, 如果查询的是第一页数据，startIndex可以省略；startIndex = (pageNum - 1) * recordPerPage
SELECT * FROM tablename LIMIT startIndex, recordPerPage;
```

DQL执行顺序
1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. ORDER BY
7. LIMIT

### DCL

```SQL
# 查询用户
USE mysql;
SELECT * FROM user;

# 创建用户 主机名可以使用%通配符，表示任意主机
CREATE USER 'username'@'host' IDENTIFIED BY 'password';

# 修改密码
ALTER USER 'username'@'host' IDENTIFIED WITH mysql_native_password BY 'newpassword';

# 删除用户
DROP USER 'username'@'host';
```

DCL权限控制

权限
- ALL, ALL PRIVELEGES
- SELECT, ALTER, DROP, DELETE, UPDATE, CREATE, INSERT

```SQL
# 查询权限
SHOW GRANTS FOR 'username'@'host';

# 授予权限
GRANT 权限列表 ON dbname.tablename TO 'username'@'host';

# 撤销权限
REVOKE 权限列表 ON dbname.tablename FROM 'username'@'host';

# 权限列表中权限用逗号分隔
# dbname和tablename可以用*表示任意
```

## 函数

### 字符串函数

- CONCAT(S1, S2, ..., Sn)
- LOWER(str)
- UPPER(str)
- LPAD(str, n, pad)
- RPAD(str, n, pad)
- TRIM(str)：去除字符串首尾空格
- SUBSTRING(str, start, len)

### 数值函数

- CEIL(x)
- FLOOR(x)
- MOD(x, y)
- RAND()
- ROUND(x, y)：返回x的四舍五入值，保留y位小数

### 日期函数

- CURDATE()
- CURTIME()
- NOW()
- YEAR(date)
- MONTH(date)
- DAY(date)
- DATE_ADD(date, INTERVAL expr type)
- DATEDIFF(date1, date2)：前 - 后

### 流程函数

- IF(val, t, f)：如果value为true，返回t，否则返回f
- IFNULL(val1, val2)
- CASE WHEN [val1] THEN [res1] ... ELSE [defalt] END
- CASE [expr] WHEN [val1] THEN [res1] ... ELSE [defalt] END

## 约束

### 1

约束是作用在表中字段上的规则，用于限制存储在表中的数据，保证数据的正确、有效性和完整性

分类：
- 非空约束 NOT NULL
- 唯一约束 UNIQUE
- 主键约束 PRIMARY KEY
- 默认约束 DEFAULT
- 检查约束 CHECK
- 外键约束 FOREIGN KEY

外键约束：让两张表的数据建立连接，从而保证数据的一致性和完整性
```SQL
CREATE TABLE tablename (
    field type,
    ...,
    [CONSTRAINT] foreignKeyName FOREIGN KEY (col) REFERENCES 主表 (col)
);

ALTER TABLE tablename ADD CONSTRAINT foreignKeyName FOREIGN KEY (col) REFERENCES 主表 (col);

ALTER TABLE tablename DROP FOREIGN KEY foreignKeyName;
```

删除/更新行为
- NO ACTION 若有外键，禁止删除/更新
- RESTRICT 同上
- CASCADE 级联删除/更新
- SET NULL 删除后，外键设置为null
- SET DEFAULT 删除后，外键设置为default (innodb不支持)

```SQL
ALTER TABLE tablename ADD CONSTRAINT foreignKeyName FOREIGN KEY (col) REFERENCES 主表 (col) ON UPDATE CASCADE ON DELETE SET NULL;
```

## 多表查询

### 1

多表关系
- 一对多：在多的一方建立外键，指向一的一方的主键
- 多对多：建立第三张表，至少设置两个外键，分别关联两张表的主键
- 一对一：多用于单表拆分，提升效率。通常将基础字段放在一张表，详情放在另一张表。在任意一方设置外键，关联另外一方的主键，并设置外键为UNIQUE

多表查询 - 作笛卡尔积
- 内连接：查询A、B交集的数据
- 外连接
  - 左外连接：查询左表所有数据及两表交集部分
  - 右外连接：查询右表所有数据及两表交集部分
- 自连接：与自身的连接查询，必须使用表别名

```SQL
# 内连接
SELECT * FROM a, b WHERE ...;
SELECT * FROM a [INNER] JOIN b ON ...;

# 左外连接
SELECT * FROM a LEFT [OUTER] JOIN b ON ...;

# 右外连接
SELECT * FROM a RIGHT [OUTER] JOIN b ON ...;

# 自连接，可以是内连接或外连接
SELECT * FROM a aliasA JOIN b aliasb ON ...;
```

联合查询：
- 多张表的列数必须一致，字段类型也要一致
- UNION ALL会将所有数据直接合并，UNION会去重
```SQL
SELECT * FROM a
UNION
SELECT * FROM b;
```

子查询

子查询外部语句可以是INSERT / UPDATE / DELETE / SELECT
```SQL
SELECT * FROM a WHERE col1 = (SELECT col1 FROM b);
```

分类：
- 子查询结果不同
  - 标量子查询：常用= >= <= <>
  - 列子查询：常用IN / NOT IN / ANY / SOME / ALL，any和some是一样的
  - 行子查询：常用= <> IN / NOT IN
  - 表子查询：常用IN
- 子查询位置不同
  - WHERE后
  - SELECT后
  - FROM后

## 事务

### 1

事务是一组操作的集合，事务会将所有操作一起向系统提交执行，要么同时成功，要么同时失败

开启事务 -> 提交事务，如果出现异常，事务回滚

MySQL执行DML语句时，隐式地自动提交事务

事务操作
```SQL
# 查看设置事务提交方式
SELECT @@autocommit;
SET @@autocommit = 0;

# 提交事务
COMMIT;

# 回滚事务
ROLLBACK;

# 开启事务
START TRANSACTION; 或 BEGIN;
```

### 事务的四大特性

ACID

- 原子性 Atomicity：事务是不可分割的最小操作单元，要么同时成功，要么同时失败
- 一致性 Consistency：事务完成时，所有数据必须保持一致
- 隔离性 Isolation：数据库提供的隔离机制，保证事务不受并发操作的独立环境下运行
- 持久性 Durability：事务一旦提交或回滚，对数据库中数据的改变是永久的


### 并发事务问题

- 脏读：一个事务读取到另一个事务未提交的记录
- 不可重复读：一个事务先后读取同一条记录，但是两次读取的数据不同
- 幻读：一个事务在读取某个范围的记录时，另一个事务这时插入了新的数据，导致第一个事务再次读取这个范围的记录时，产生幻行phantom data，多了新的数据

### 事务隔离级别

- READ UNCOMMITTED：脏读、不可重复读、幻读
- READ COMMITTED：不可重复读、幻读
- REPEATABLE READ：幻读
- SERIALIZABLE：无

事务隔离级别越高，安全性越高，性能越差

```SQL
# 查看事务隔离级别 MySQL默认Repeatable Read
SELECT @@ TRANSACTION_ISOLATION;

# 设置事务隔离级别
SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL [4种]
```

## 存储引擎

### 1

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是基于表的，而不是基于库的，所以存储引擎也可以被称为表类型

```SQL
# 建表时指定存储引擎
CREATE TABLE (
    col1 type1,
    ...
) ENGIEN = InnoDB;
```

InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在MySQL 5.5后，InnoDB是默认的MySQL存储引擎。

特点：
- DML操作遵循ACID，支持事务
- 行级锁，提高并发性能
- 支持外键约束
- xxx.idb文件，innoDB引擎的每张表都有一个idb文件，存储该表的结构（frm、sdi）、数据和索引

逻辑存储结构
- TableSpace：表空间
- Segment：段
- Extent：区 最大1M，能存16个Page
- Page：页 最大16K
- Row：行，包含Trx id，Roll Pointer，各列

MyISAM特点
- 不支持事务、不支持外键
- 支持表锁，不支持行锁
- 访问速度快
- 文件：xxx.MYD（数据）, xxx.MYI（索引）, xxx.sdi（表结构）

Memory特点
- Memory引擎存储数据在内存中，只能作为临时表或缓存使用
- 内存存放
- hash索引（默认）
- xxx.sdi 存储表结构信息

存储引擎的选择：应根据业务特点和需求，复杂的系统还可以根据不同场景选择
- InnoDB：适用于对数据完整性要求高，并发性能要求高，除了读写外，更新、删除操作比较多的场景
- MyISAM：以读和写操作为主，对事务完整性、并发性能要求不高
- Memory：访问速度快，但是对表的大小有限制

InnoDB和MyISAM对比：
- InnoDB 支持行级别的锁粒度，MyISAM 不支持，只支持表级别的锁粒度。
- MyISAM 不提供事务支持。InnoDB 提供事务支持，实现了 SQL 标准定义了四个隔离级别。- MyISAM 不支持外键，而 InnoDB 支持。
- MyISAM 不支持 MVCC，而 InnoDB 支持。
- 虽然 MyISAM 引擎和 InnoDB 引擎都是使用 B+Tree 作为索引结构，但是两者的实现方式不太一样。
- MyISAM 不支持数据库异常崩溃后的安全恢复，而 InnoDB 支持。
- InnoDB 的性能比 MyISAM 更强大。

## 索引

### 1

索引是MySQL中的一种高效、有序的数据结构

无索引：进行全表扫描

索引优缺点
- 优点
  - 提高检索数据的效率，降低IO
  - 通过索引列对数据进行排序，降低CPU消耗
- 缺点
  - 索引占据一部分空间
  - 降低了update insert delete等的效率

索引结构
- B+Tree索引：最常见的索引类型，大部分存储引擎都支持
- Hash索引：底层通过hash表实现，只支持精确匹配索引列的查询，不支持范围查询
- R-tree（空间索引）：MyISAM中的特殊索引，针对地理位置
- Full-Text（全文索引）：倒排索引，快速匹配文档，类似于Lucene、ES

B-Tree 多路平衡查找树
- 最大度数m是最多有几个子节点，节点中能存储m-1个key和m个指针

B+Tree
- 所有数据都存储在叶子节点，非叶子节点只用于索引查找
- 所有叶子节点之间形成一个单向链表
- MySQL中的B+Tree，增加了一个指向相邻叶子节点的链表指针，形成了带有顺序指针的树，提高区间访问效率

InnoDB选择B+Tree索引结构的原因
- 相对于二叉树，层级更少，检索效率高
- 对于B-tree，叶子节点和非叶子节点都要保存数据，而一页大小有限，导致能存储的键值减少，指针也减少，存储相同的数据，要增加树的高度
- 相对于hash，B+Tree支持范围查询及排序

### 索引的分类

- 主键索引：PRIMARY KEY
- 唯一索引：UNIQUE
- 常规索引
- 全文索引：FULLTEXT

根据存储形式
- 聚集索引
  - 将数据存储与索引放到一起，索引结构的叶子节点保存了行数据
  - 必须有，且只有一个
  - 选取规则
    - 如果存在主键，主键索引就是聚集索引
    - 如果不存在主键，会选取第一个UNIQUE索引作为聚集索引
    - 如果不存在UNIQUE，会自动生成一个rowid作为隐藏的聚集索引
- 二级索引：
  - 将数据与索引分开存储，索引结构的叶子节点关联的是对应主键
  - 可以有多个

索引的查询方式：回表查询
- 首先在二级索引中找到数据对应的主键
- 通过主键回到聚集索引中找到行数据
- 返回行数据

B+tree的高度计算

假设一页16K，一行数据占1K。InnoDB的指针占6字节，主键BIGINT占8字节。求高度为2和高度为3的索引能存储的数据量。

8 * n + 6 * (n + 1) = 16 * 1024

解得 n = 1170，一个节点可以有1170个键值，1171个指针，即1171个子节点。则高度为2，数据量为1171 * 16 = 18736；高度为3，数据量为1171 * 1171 * 16 = 21939856

### 索引语法

```SQL
CREATE [UNIQUE|FULLTEXT] INDEX indexName ON tablename (indexColumnName, ...);

SHOW INDEX FROM tablename;

DROP INDEX indexName ON tablename;
```

### SQL性能分析

SQL性能分析
- SQL执行频率：可以通过以下语句查看
    ```SQL
    # 七个下划线
    SHOW [SESSION|GLOBAL] STATUS LIKE 'Com_______';
    ```
- 慢查询日志：记录了所有查询时间长于指定参数（long_query_time，默认10秒）的SQL语句的日志；MySQL中默认未开启
- profile详情：show profiles能在SQL优化时帮助我们了解时间在哪部分消耗了
    ```SQL
    # 查看每一条SQL耗时的基本情况
    SHOW PROFILING;
    # 查看指定query_id的SQL的各阶段耗时
    SHOW PROFILE FOR query query_id;
    # 查看指定query_id的SQL的CPU使用情况
    SHOW PROFILE CPU FOR query query_id;
    ```
- explain执行计划：explain命令或desc命令获取MySQL执行SELECT语句的信息，包括在查询中表如何连接和连接的顺序
    ```SQL
    EXPLAIN|DESC SELECT * FROM tablename WHERE xxx;
    ```
  - id：select查询的序列号，表示查询中执行select查询或操作表的顺序。id相同，从上到下执行；id不同，越大越先执行
  - select_type：表示SELECT查询的类型，常见的有SIMPLE（简单表，不需要表连接或子查询），PRIMARY（主查询，即外层的查询），UNION（UNION中的第二个或后面的查询），SUBQUERY（SELECT或WHERE中包含的子查询）
  - type：表示连接类型，性能由好到差是：NULL, system, const, eq_ref, ref, range, index, all
  - possible_key：显示可能会用在这张表上的索引，一个或多个
  - key：实际使用的索引，如果没有使用，显示NULL
  - key_len：表示索引中使用的字节数，该值是索引字段最大可能长度，并非实际使用长度，在不损失精度的前提下，越短越好
  - rows：MySQL认为要查询的行数，在innoDB表中是个估计值
  - filtered：表示返回结果的行数占需读取行数的百分比，越大越好

### 索引的使用原则

索引的最左前缀法则：如果索引了多列（联合索引），要遵循最左前缀法则。查询从索引的最左列开始，并且不跳过中间列，如果跳过某一列，索引将部分失效（跳过的列的后面的列）

范围查询：联合索引中，出现范围查询（<, >），范围查询右侧的列失效。业务允许的话，尽量使用>=, <=，between，这时范围右侧的列不会失效；<=和>=时，会利用=的值，进行索引匹配，所以会用到后面的列的索引

索引失效情况：
- 在索引列上进行运算，例如substring
- 字符串不加引号（类型转换）
- 模糊查询：如果是尾部模糊匹配（like 'A%'），不会失效；如果是头部模糊匹配（like '%A'），会失效
- or连接的条件：如果or前的条件中列有索引，or后面无索引，则都不会使用索引；如果or前后都有索引，才会使用索引
- 数据分布影响：如果MySQL评估使用索引比全表扫描慢，则不会使用索引

SQL提示：在SQL语句中加入人为提示信息，来达到优化操作的目的
```SQL
EXPLAIN SELECT * FROM tablename USE INDEX(index_name) WHERE xxx;
EXPLAIN SELECT * FROM tablename IGNORE INDEX(index_name) WHERE xxx;
EXPLAIN SELECT * FROM tablename FORCE INDEX(index_name) WHERE xxx;
```

覆盖索引：查询使用了索引，且需要返回的列，在索引中已经被覆盖，减少select *
- profile中extra字段为using index condition：表示查找使用了索引，但需要回表查询
- profile中extra字段为using where; using index：表示查找使用了索引，但是需要的数据都能在索引列中找到，不需要回表查询，性能高

前缀索引：将字符串的前缀提取出来建立索引，可以节约索引空间

```SQL
# n表示提取前多少个字符建立索引
CREATE INDEX idx_xxx ON tablename(column(n));
```

前缀长度通过索引的选择性决定。选择性指不重复的索引值（基数）和数据表的记录总数的比值。比值越大，查询效率越高，唯一索引的选择性是1
```SQL
# 求选择性
SELECT COUNT(DISTINCT email) / COUNT(*) FROM user;
SELECT COUNT(DISTINCT SUBSTRING(email, 1, 5)) / COUNT(*) FROM user;
```

单列索引和联合索引
- 单列索引：只包含一列
- 联合索引：包含多列
- 业务场景中，如果涉及查询多列，推荐建立并使用联合索引
- 多条件联合查询时，MySQL会评估使用哪个索引效率高，不一定会使用联合索引

### 索引设计原则

- 针对于数据量较大（大于100w），且查询比较频繁的表建立索引。
- 针对于常作为查询条件 (where)、排序(order by)、分组(group by) 操作的字段建立索引
- 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
- 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引
- 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
- 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率
- 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询

## SQL优化

### 插入数据

insert优化
- 批量插入
- 手动提交事务：start transaction; insert xxx;  commit;
- 主键顺序插入：性能高于乱序插入
- load大批量插入数据：
    ```SQL
    # 连接客户端
    mysql --local-infile -u root -p
    # 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
    set global local_infile = 1;
    # 执行load指令，将准备好的数据导入表中
    load data local infile '/root/filename' into table tablename fields terminated by ',' lines terminated by '\n';
    ```

### 主键优化

InnoDB数据组织方式：表数据根据主键顺序存储，这种存储方式的表称为索引组织表（Index Organized Table, IOT）

页分裂：页可以为空，也可以填充一半，也可以填充100%。每个页包含了2~N条数据（如果一行数据过大，会行溢出），根据主键排列。

页分裂操作，当主键乱序插入且当前页没有空间，会申请新的页，将当前页的一般移动到新页中，并将新数据插入到对应位置

页合并：当删除一行记录时，并没有物理删除，而是被标记为删除，并且它的空间可以被其他记录声明使用

当页中删除的记录达到MERGE_THRESHOLD时（默认为50%，可以在创建表或索引时设置），InnoDB会寻找最近的页（前/后），检查是否可以将两页合并

主键设计原则
- 尽量降低主键长度
- 插入时，尽量选择顺序插入，使用AUTO_INCREMENT自增主键
- 尽量不要使用UUID或自然主键，如身份证号，作主键，会导致乱序插入
- 业务操作时，尽量避免修改主键

### ORDER BY 优化

排序方式
- using filesort：在排序缓冲区sort buffer完成排序，所有不是通过索引排序的都是filesort排序
- using index：通过有序索引直接扫描返回排序结果，效率高
- 创建索引，并且都asc排序：using index
- 创建索引，并且都desc排序：using backward index
- 创建索引，但是一个升序一个降序：using filesort
- 创建索引，但是排序列顺序不符合最左前缀法则：using filesort, using index
- 创建索引指定asc，desc：using index
- using index的前提，必须是覆盖索引

优化方式
- 根据排序字段建立合适的索引，并遵循最左前缀法则
- 尽量使用覆盖索引
- 多字段排序，有升序，有降序，需要注意联合索引在创建时的规则
- 如果不可避免地出现filesort，在大数据量排序时，可以适当增加排序缓冲区大小sort_buffer_size（默认256KB）

### GROUP BY 优化

分组时通过索引提高效率

- 遵循最左前缀法则
- 如果where中指定索引列=，则group by中在没有where中列的情况下，也会使用索引

### limit 优化

如果limit 2000000, 10  意味着MySQL需要排序前2000000条记录，再返回2000000 - 2000010的记录

优化
- 创建覆盖索引，可以通过覆盖索引+子查询的形式优化

```SQL
SELECT s.* FROM user s, (SELECT id FROM sku ORDER BY id LIMIT 2000000, 10) a WHERE s.id = a.id;
```

### count 优化

- MyISAM把表的总行数记录在了磁盘，count(*)直接返回
- InnoDB需要一行行读取，再累计计数

优化
- 自己计数：利用redis，insert delete时修改总数
- COUNT(pk)：取出每一行的主键，返回服务层。服务层按行累加（主键不可能为null）
- COUNT(字段)：
  - 没有not null约束：遍历整张表取出字段，返回服务层。服务层判断是否为null，再累加
  - 有not null约束：遍历整张表取出字段，返回服务层，直接按行累加
- COUNT(1)：遍历整张表，但是不取值。服务层对返回的每一行，放入1，再累加
- COUNT(*)：InnoDB不会把全部字段取出，而是做了优化，服务层直接按行累加
- 按效率排序：count(field) < count(pk) < count(1) ≈ count(*)

### update 优化

InnoDB的行锁是针对索引加的锁，不是针对记录加的锁。并且该索引不能失效，否则会从行锁升级为表锁。

update的where中应使用索引列，否则行锁会升级为表锁

## 视图/存储过程/触发器

### 视图

视图view，是虚拟存在的表，其中的行列数据来源于定义视图时查询的表，并在使用视图时动态生成

```SQL
# 创建 或 修改视图
CREATE [OR REPLACE] VIEW viewname[colname] AS SELECT xxx [WITH [CASCADE|LOCAL] CHECK OPTION];
# 查看创建视图语句
SHOW CREATE VIEW viewname;
# 查看视图
SELECT * FROM viewname ...;
# 修改视图
ALTER VIEW viewname[colname] AS SELECT xxx [WITH [CASCADE|LOCAL] CHECK OPTION];
# 删除
DROP VIEW [IF EXISTS] viewname [viewname] ...;
```

视图的检查选项：当使用`WITH CHECK OPTION`创建视图后，向视图中增删改数据时，MySQL会检查数据是否符合视图的定义。MySQL允许基于另一个视图创建视图，他还会检查依赖视图中的规则以保持一致性。提供了两个选项：
- CASCADED：默认值，将检查选项加在依赖的视图中，不论依赖的视图有没有定义with check option
- LOCAL：与CASCADED相反，不将检查选项加在依赖的视图中

视图的更新，要求视图中的行和基础表中的行一一对应，如果试图包含以下任意一项，则不可更新：
- 聚合函数或窗口函数 min max sum count 
- distinct
- group by
- having
- union 或 union all

### 存储过程

封装和重用多条SQL
```SQL
DELIMITER $$
CREATE PROCEDURE pname(param p1)
BEGIN
  xxx
END
;

CALL pname(p1);
```

变量：
- 系统变量
  - 全局变量global
  - 会话变量session
- 用户定义变量
  - 通过@访问
- 局部变量
  - 访问之前需要DECLARE声明，范围在BEGIN到END之间
```SQL
# 系统变量
SHOW [GLOBAL|SESSION] VARIABLES;
SHOW [GLOBAL|SESSION] VARIABLES LIKE 'xxx';
SELECT @@[GLOBAL|SESSION].vname;

SET [GLOBAL|SESSION] vname = val;
SET @@[GLOBAL|SESSION].vname = val;

# 用户定义变量
SET @vname = expr, ...;
SET @vname := expr;
SELECT @vname := expr;
SELECT col INTO @vname FROM tablename;

# 局部变量
DECLARE vname type [DEFAULT ...];

# if
IF xx THEN
..
ELSEIF xxx THEN
...
ELSE
...
END IF;

# 参数
CREATE PROCEDURE p (IN a int, OUT b int, INOUT c int)

# while
while xx do
xxx
end while

# repeat
repeat
  xxx
  until yyy
end repeat;

# loop，可使用`leave label`退出循环，`iterate label`跳过当前
[begin_lavel:] loop
  xxx
end loop [end_label];

```

游标
```SQL
DECLARE cname CURSOR FOR 查询语句;

OPEN cname;

FETCH cname INTO v1[, v2];

CLOSE cname;
```

## 锁

### 概述

锁是协调并发访问资源（数据库中的数据）的机制。

锁的分类
- 全局锁：锁定数据库中的所有表
- 表级锁：每次操作锁定数据库中的某一张表
- 行级锁：每次操作锁定表中的一条记录

### 全局锁

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被阻塞。

其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。

```SQL
# 加全局锁
flush tables with read lock;
# 备份命令 不是SQL，应在外部命令行执行
mysqldump -uroot -p1234 dbname > dbname.sql;
# 解锁
unlock tables;
```

加全局锁的弊端
- 主库加锁，业务停摆
- 从库加锁，无法同步主库的binlog，数据不一致
- 解决方案
  - InnoDB引擎中，可以在备份时加入--single-transaction参数来完成不加锁的一致性数据备份

### 表级锁

表级锁，每次操作锁定整张表。锁定粒度大，发生锁冲突的概率高，并发低。应用在MyISAM、InnoDB、BDB等引擎中。

表级锁分类
- 表锁
  - 表共享读锁read lock
  - 表独占写锁write lock
  - 加锁：
    ```SQL
    lock tables tablename read/write;    
    ```
  - 释放锁：unlock tables 或 关闭客户端连接
  - 读锁不堵塞其他client的读，写锁会堵塞其他client的读和写
- 元数据锁(meta data lock, mdl)
  - MDL加锁过程是系统自动控制，在访问一张表时会加锁。主要作用是维护元数据的一致性，在表上有未提交事务时，不能修改元数据。防止DML和DDL冲突
  - 对表进行crud时，会自动加shared_read/shared_write共享锁。进行表结构修改时，会自动加MDL互斥锁。
- 意向锁
  - 为避免DML在执行时，行锁和表锁的冲突，InnoDB中加入了意向锁，使得表锁不用检查每行是否加了行锁，减少表锁的检查
  - 意向共享锁IS：与表锁共享锁（read）兼容，与表锁排他锁（write）互斥
    ```SQL
    # 添加语句，该命令也是添加行级共享锁的命令
    select ... lock in share mode;
    ```
  - 意向排他锁IX：与表锁共享锁（read）和表锁排他锁（write）都互斥
    ```SQL
    # 添加语句，该命令也是添加行级排他锁的命令
    insert/delete/update/select ... for update;
    ```
  - 意向锁之间不互斥

### 行级锁

行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生所冲突的概率最低，并发最高，应用在InnoDB中。

InnoDB的数据是基于索引组织的，行锁是对索引上的索引项加锁来实现的，而不是对记录加的锁。

分类：
- 记录锁record lock：锁定单个行记录的锁，防止其他事务进行update和delete。在RC、RR隔离级别下都支持
  - 共享锁S：允许事务读一行，S与S共享，与X互斥
  - 排他锁X：允许事务更新数据，X与S和X都互斥
  - insert、update、delete自动加排他锁
  - select默认不加任何锁，`select ... lock in share mode`加共享锁，`select ... for update`加排他锁
  - 默认情况下，InnoDB在RR级别运行，使用next-key lock进行搜索和扫描索引，防止幻读
    - 针对唯一索引扫描时，对已存在的记录进行等值匹配时，临键锁会自动优化为行锁
    - 不按照索引检索数据，InnoDB会锁住整张表，行锁升级为表锁
- 间隙锁gap lock：锁住索引记录的间隙（左开右闭），不包含该记录，确保索引记录不变，防止其他事务在间隙进行insert，产生幻读。在RR隔离级别下支持。锁住的间隙是第一条索引记录之前的范围，或最后一条索引记录之后的范围
  - 间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存
- 临键锁Next-Key Lock：索引记录的行锁 + 记录前的间隙的间隙锁。在RR隔离级别下支持
  - 假设有四条索引记录10，11，13，20，临键锁对该索引的锁覆盖了以下区间
    ```
    (negative infinity, 10]
    (10, 11]
    (11, 13]
    (13, 20]
    (20, positive infinity)
    ```
  <!-- - 索引上的等值查询(唯一索引)，给不存在的记录加锁时，优化为记录锁。
  - 索引上的等值查询(普通索引)，向右遍历时最后一个值不满足查询需求，next-key lock退化为间隙锁
  - 索引上的范围查询(唯一索引)，会访问到不满足条件的第一个值为止，在这个不满足条件的值前后加临键锁（该记录之前到范围的锁，无穷大到该记录的锁）。 -->

- 唯一索引
  - 精确等值查询 `where id = 10`：
    - `id 10`存在，则next-key lock退化为行锁，不加间隙锁 （如果where中是多列唯一索引中的一些列，依然会有间隙锁）(This does not include the case that the search condition includes only some columns of a multiple-column unique index; in that case, gap locking does occur.)；
    - `id 10`不存在，则next-key lock退化为间隙锁，锁住该不存在id前后的存在的id之间的范围
  - 范围查询：锁住where条件中的范围，包括范围中的记录及间隙，行锁+间隙锁；
    - `where id > 99`，如果id数据为`97`和`101`，那么对索引加的是`(97,101]`的next-key lock和`(101,supremum pseudo]`的next-key lock
    - `where id >= 97`，如果id数据为`97`和`101`，那么`id 97`的主键索引的next-key lock会退化成记录锁，还会加`(97,101]`的next-key lock和`(101,supremum pseudo]`的next-key lock
    - `where id < 99 / where id <= 99`，如果id数据为`97`和`101`，那么对索引加的是`(-∞,97]`的next-key lock；接着扫描到`id 101`，由于该记录不满足where条件，所以该记录的锁会退化成`(97,101)`间隙锁
    - `where id <= 101`，如果id数据为`97`和`101`，那么首先找到`id 97`，加入`(-∞,97]`的next-key lock；接着找到`id 101`，加入`(97,101]`的next-key lock；主键索引有唯一性，所以停止扫描
    - `where id < 101`，如果id数据为`97`和`101`，那么首先找到`id 97`，加入`(-∞,97]`的next-key lock；接着找到`id 101`，该记录是第一条不满足where的记录，该记录的锁退化为`(97,101)`间隙锁；找到了第一条不满足where的记录，停止扫描
  - 不通过索引检索：全表间隙加间隙锁，全表记录加行锁
- 非唯一索引：此时存在主键索引和该非唯一索引两个索引，在加锁时，会对这两个索引同时加锁；但在对主键索引加锁时，只有满足查询条件的记录才会对其主键索引加锁
  - 等值查询
    - `where age = 10`，`age`数据为`8, 11, 11, 20`，`id`数据为`10, 20, 30, 40`，那么扫描到第一个不符合where的非唯一索引记录`age 11`，该索引的next-key lock会退化为`(8,11)`间隙锁；由于记录不存在，主键索引不会加锁
      - 考虑插入的情况，二级索引是按照age的大小顺序排列的，在age相同时，则按照id从小到大排列；插入时，如果`age=8`且`id`小于该age对应的`id 10`则可以插入，否则不行，对`age=11`同理
      - 此时data_locks表中的`LOCK_DATA`字段显示为`11 20`，其中`11`是`age`，`20`是对应的`id`，说明插入`age=11`的记录时，不能插入`id<20`的数据
    - `where age = 11`，`age`数据为`8, 11, 11, 20`，那么找到`age 11`，加入`(8,11]`的next-key lock，对应的id加记录锁；找到下一个`age 11`，对应的id加记录锁；找到`age 20`，第一个不符合条件的二级索引记录，该索引记录的next-key lock退化为`(11,20)`间隙锁；停止查询
  - 范围查询：非唯一索引的next-key lock不会退化成间隙锁和记录锁
    - `where age >= 11`，`age`数据为`8, 11, 11, 20`，那么扫描到`age 11`，等值查询成功，对二级索引记录加`(8,11]`next-key lock，对`age 11`对应的主键索引加记录锁；扫描到第二个`age 11`，对应的主键索引加记录锁；扫描到`age 20`，对二级索引记录加`(11,20]`next-key lock，对`age 20`对应的主键索引加记录锁；扫描到最后，对二级索引记录加`(20,supremum pseudo]`next-key lock；停止查询
- 非索引：执行 update、delete、select ... for update 等具有加锁性质的语句，如果不走索引，则会导致全表扫描，对每一条索引记录都加next-key lock，相当于锁住整张表

查看当前锁
```SQL
select OBJECT_SCHEMA, OBJECT_NAME, INDEX_NAME, LOCK_TYPE, LOCK_MODE, LOCK_STATUS, LOCK_DATA FROM  performance_schema.data_locks;
```
- LOCK_TYPE字段：
  - TABLE：表级锁
  - RECORD：行级锁
- LOCK_MODE字段：
  - IX：表级意向锁
  - X：X型 next-key lock
  - X, GAP：X型 间隙锁
  - X, REC_NOT_GAP：X型 记录锁
- LOCK_DATA字段：
  - supremum pseudo-record：正无穷
  - 100：表示锁的右边界，锁的左边界是该属性的的上一条记录的值

行锁的测试数据
```

```

## InnoDB引擎

### 逻辑存储结构

从高到低：
- 表空间tablespace
- 段segment
- 区extent
- 页page
- 行row：包含trx_id，事务id；undo指针，可以找到修改前的记录

### 架构

InnoDB主要分为内存架构和磁盘架构
- 内存架构
  - Buffer Pool：缓冲池，缓存磁盘上经常操作的数据，进行增删改时，先操作缓存，再以一定频率和规则将数据刷新到磁盘，减少了磁盘IO；以page为基本单位，底层使用链表管理page，可以分为三类
    - free page：空闲page，未被使用
    - clean page：被使用的page，数据未被修改
    - dirty page：脏页，被使用过且数据被修改，与磁盘数据不一致
  - Change Buffer：更改缓冲区，针对非唯一二级索引页，执行DML时，如果页不在Buffer Pool中，不会直接操作磁盘，会将数据存在Change Buffer中，未来数据被读取时，合并放入Buffer Pool，再统一合并到磁盘中；非唯一二级索引会造成随机插入，更新和删除会影响不相邻的页，Change Buffer可以减少磁盘IO
  - Adaptive Hash Index：自适应hash索引，优化Buffer Pool的查询；InnoDB会监控对数据对访问，如果发现使用hash可以提高效率，会建立hash索引
    - 参数：adaptive_hash_index
  - Log Buffer：日志缓冲区，存储redo log和undo log，默认大小16MB，日志定期刷新到磁盘中。若需要增删改多行数据，可以增加缓冲区大小。
    - 参数：innodb_log_buffer_size / innodb_flush_log_at_trx_commit
      - innodb_flush_log_at_trx_commit设置为0，redo log每秒写一次并向磁盘中刷新一次
      - 设置为1，每次事务提交都写入redo log并向磁盘中刷新
      - 设置为2，每次事务提交都写入redo log，并每秒向磁盘中刷新一次
- 磁盘架构
  - System Tablespace：系统表空间时Change Buffer的存储区域
  - File-Per-Table Tablespaces：每个表的文件表空间包含单个InnoDB表的数据和索引，并存储在磁盘上的单个文件中
    - 参数：innodb_file_per_table
  - General Tablespaces：通用表空间，需要通过`CREATE TABLESPACE xxx ADD DATAFILE filename ENGINE=innodb;`创建通用表空间，创建表时可以指定该表空间
  - Undo Tablespaces：撤销表空间。InnoDB会自动创建两个，默认大小16MB，存储undo log
  - Temporary Tablespaces：会话临时表空间和全局临时表空间，存储用户创建的临时表等数据
  - Doublewrite Buffer Files：双写缓冲区，innoDB将数据刷新到磁盘前，会先写入该区域，便于系统异常时恢复数据
  - Redo Log：redo log，用来实现事务的持久性。由两部分组成：重做日志缓冲redo log buffer，重做日志文件redo log，前者在内存中，后者在磁盘中。事务提交后会把修改信息写入redo log，用于在刷新脏页到磁盘出现异常时，进行数据恢复

后台线程：
- Master Thread：核心线程，负责调度线程、异步刷新缓冲池数据到磁盘、刷新脏页、合并插入内存、回收undo页
- IO Thread：采用aio，负责IO请求的回调
  - read thread：默认4个，负责读操作
  - write thread：默认4个，负责写操作
  - log thread：默认1个，负责将日志缓冲区刷新到磁盘
  - insert buffer thread：默认1个，负责将写缓冲区刷新到磁盘
- Purge Thread：回收事务已经提交了的undo log
- Page Cleaner Thread：协助Master Thread刷新脏页到磁盘

### 事务原理

ACID四个特性，其中ACD是由redo log和undo log保证的，I是由锁和MVCC机制保证的

- redo log：记录的是事务提交时数据页的物理修改，用来实现事务的持久性
  - 事务进行增删改操作，内存中Buffer Pool数据页被修改，出现脏页
  - innodb将修改记录写入redo log buffer，再刷新到磁盘中redo log file
  - 内存中脏页刷新到磁盘时，如果出现错误，那么使用redo log file恢复数据
  - 若没有错误，则定期清理redo log
  - 这种方式叫WAL (Write-Ahead Logging)
  - 硬盘上以日志文件组的形式存在，采用环形数组写入
- undo log：用于记录数据被修改前的操作，作用：提供回滚和MVCC
  - undo log记录的是逻辑日志，与redo log不同
  - 执行delete，会记录一条对应的insert；执行update，会记录一条相反的update；当执行rollback时，就从undo log中取出记录并执行
  - undo log销毁：事务提交时，undo log并不会被立即删除，因为可能还涉及到MVCC
  - undo log存储：采用段的形式进行管理和记录，存储在rollback segment回滚段中，内部包含1024个undo log segment
- binlog：记录的是逻辑日志，只要发生表数据更新，就会产生binlog，可用于MySQL的数据备份、主备、主主、主从
  - binlog格式
    - statement：记录原始语句，但是遇到`now()`函数时会出错
    - row：记录语句+具体操作数据，需要通过`mysqlbinlog`解析；该方法占用内存大，恢复与同步时消耗IO资源
    - mixed：前两者的混合，MySQL判断SQL是否会引起不一致，是则用row，否则用statement
  - binlog写入：事务执行中将log写入binlog cache，事务提交后将binlog cache写到binlog文件

两阶段提交
- 为了解决两份日志之间的逻辑一致问题，将redo log的写入拆成了两个步骤`prepare`和`commit`
  - `prepare`：事务执行过程中写入redo log，处于prepare状态
  - `commit`：事务提交时，先写binlog，binlog写完成后，将redo log设置为commit状态
  - 若binlog写入异常，数据也是一致的。因为MySQL后续根据redo log恢复数据时，发现redo log依然是`prepare`状态，并且没有对应的binlog，会回滚该事务
  - 若设置redo log为commit时失败，MySQL发现redo log不是`commit`时，会检查是否有对应的binlog，若有，也会提交事务


### MVCC

基本概念：
- 当前读：读取的是记录的最新版本，为了防止记录被并发事务修改，还要对记录加锁。对于日常操作，如`select xxx for update`, `select xxx lock in share mode`, `update`, `delete`, `insert`，都是当前读
- 快照读：简单的不加锁的select就是快照读，读取是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读
  - Read Committed：每次select，都生成一个快照读
  - Repeatable Read：事务开启后的第一个select是快照读
  - Serializable：快照读会退化为当前读
- MVCC：Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为MySQL实现MVCC提供了非阻塞读功能；MVCC的具体实现，依赖于数据库记录的三个隐式字段、redo log、undo log

实现原理：
- 隐藏字段
  - DB_TRX_ID：最近修改事务id，记录插入这条记录或最后一次修改这条记录的事务id
  - DB_ROLL_PTR：回滚指针，指向这条记录的上一个版本，配合undo log进行回滚
  - DB_ROW_ID：隐藏主键，若没有指明pk，则生成该隐藏字段
- undo log：
  - 回滚日志，在update、delete、insert时产生的便于回滚的日志
  - insert时，产生的undo log只在回滚时需要，事务提交后，可立即删除
  - update和delete时，undo log不仅在回滚时需要，在快照读时也需要，不会被立即删除
  - undo log版本链：不同事务或相同事务对同一条记录进行修改，会导致该记录的undo log生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。
- Read View：读视图，是快照读SQL执行时MVCC提取数据的依据，记录并维护当前系统活跃的事务id
  - 包含四个核心字段
    - m_ids：当前活跃的事务id集合
    - min_trx_id：最小活跃事务id
    - max_trx_id：预分配事务id，当前最大事务id+1
    - creator_trx_id：ReadView创建者的事务id
  - 版本链数据访问规则：`trx_id`是当前事务的id，顺着undo log版本链向后找，不断用每条记录的`trx_id`与下列规则比较，直到找到能访问的一条
    - `trx_id == creator_trx_id`  数据是当前事务修改的，允许访问
    - `trx_id < min_trx_id` 数据已经提交了，允许访问
    - `trx_id > max_trx_id` 该事务是在ReadView之后开启的，不允许访问
    - `min_trx_id <= trx_id <= max_trx_id` 如果trx_id不在m_ids中，是可以访问该版本的，说明数据已经提交，允许访问
  - 不同隔离级别生成ReadView的时机
    - Read Committed：每次快照读生成一次Read View
    - Repeatable Read：第一次快照读生成Read View，后续复用

Purge线程清理Undo日志时，判断Undo日志中的trx_no属性的值小于数据库中当前最早创建的ReadView的m_low_limit_no属性的值，该Undo日志就可以删除。

- 原子性：undo log
- 一致性：undo log + redo log 通过原子性、隔离性、持久性共同实现一致性
- 隔离性：锁+MVCC
- 持久性：bin log + redo log

### MySQL自增值不连续

自增值不连续的 4 个场景：
- 自增初始值和自增步长设置不为 1
- 唯一键冲突
- 事务回滚
- 批量插入（如 insert...select 语句）

## 日志

### 错误日志

错误日志记录了数据库启动、停止、发生任何严重故障时的信息。

```SQL
SHOW VARIABLES LIKE '%log_error%';
```

### 二进制日志

binlog记录了所有DDL和DML语句，但不包括查询（SELECT、SHOW）语句

作用：
- 故障恢复
- 主从复制

```SQL
SHOW VARIABLES LIKE '%log_bin%';
```

binlog分为三种格式：ROW、STATEMENT、MIXED（默认STATEMENT，某些情况会变为ROW）

### 查询日志

查询日志记录了客户端中所有的操作语句，默认未开启。

```SQL
SHOW VARIABLES LIKE '%general%';
```

### 慢查询日志

慢查询日志记录了所有执行时间超过参数`long_query_time`且扫描记录数不超过`min_examined_row_limit`的操作语句，默认未开启。

```SQL
# 开启慢查询日志
slow_query_log=1
# 慢查询阈值
long_query_time=2
# 记录执行较慢的管理语句
log_slow_admin_statements=1
# 记录执行较慢的未使用索引的语句
log_querys_not_using_indexes=1
```

## 主从复制

### 主从复制

主从复制是指将主数据库的DDL和DML语句通过binlog传到从库中，从库根据binlog进行重新执行，从而使主库和从库的数据保持同步。

MySQL支持一台主库向多台从库进行复制，也支持从库向从库进行复制，形成链状复制。

MySQL主从复制的优点：
- 主库出现问题，快速从主库切换到从库
- 读写分离，降低主库的压力
- 可在从库中执行备份，以避免备份期间影响主库服务，但可能存在数据不一致

主从复制的原理：
1. Master将执行的操作（DML、DDL）写入binlog中
2. Slave通过IOthread读取Master的binlog，写入自己的中继日志Relaylog中
3. Slave通过SQLthread，5重做relaylog中的操作，将改变反映到自己的数据上

## 分库分表

### 拆分策略

- 垂直拆分：
  - 垂直分库：以表为依据，根据不同的业务将不同的表拆分到不同库中
    - 每个库中的表结构都不一样
    - 每个库中的数据都不一样
    - 所有库的并集构成全量数据
  - 垂直分表：以字段为依据，根据字段属性将不同的字段拆分到不同的表中
    - 每个表的结构都不一样
    - 每个表的数据都不一样，一般通过一列（主键/外键）来关联
    - 所有表的并集构成全量数据
- 水平拆分：
  - 水平分库：
  - 水平分表