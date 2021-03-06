---
title: SQL总结
tags: [sql]
description: SQL总结
keywords: SQL
author: sunbufu
date: 2018-06-06T00:00:00+00:00
draft: false
---

# 一、SQL定义
SQL语言的全称是Structured Query Language，即结构化查询语言。

## 1、SQL分类
### 1）DQL（Data Query Language 数据查询语言）
查询语句只有一种，即`SELECT`语句。

### 2）DML（Data Manipulation Language 数据操作语言）
包括：`INSERT、UPDATE、DELETE`等常用语句。

### 3）DDL（Data Definition Language 数据定义语言）
包括：`CREATE（创建），ALTER（修改），DROP（删除），RENAME（重命名），TRUNCAT（截断）`等常用语句。

### 4）DCL（Data Control Language 数据控制语言）
包括：`GRANT（权限分配），REVOKE（权限回收），TRANSACTION（事务处理）`。

### 5）事务控制语句TRANSACTION（事务处理）
包括：`COMMIT（提交）、ROLLBACK（回滚）`等常用语句。

***
# 二、DQL（Data Query Language 数据查询语言）
## 1、列查询
### 1）单列查询

```sql
SELECT 字段 [AS] [别名] FROM 表名;
--别名显示时，AS可以省略，例如：
SELECT EMPNO AS "ID" FROM EMP;--"ID"双引号表示原样显示
```

### 2）多列查询

```sql
SELECT 字段 [AS] [别名], 字段2 [AS] [别名2] ... 字段n [AS] [别名n] FROM 表名;
```
### 3）所有列查询

```sql
SELECT * FROM 表名;
```

### 4）去重
关键字：**DISTINCT**（明显的，独特的）

```sql
SELECT DISTINCT 字段 [AS] [别名] FROM 表名;
```

## 2、行查询（限制查询、条件查询）

```sql
SELECT    字段, 组函数...
FROM    表名
[WHERE    条件]
[GROUP BY  ASC|DESC]
[HAVING 条件]
[ORDER BY  字段];
--SQL语句执行过程：
----读取FROM子句中的基本表、视图的数据，[执行笛卡尔积操作]。
----选取满足WHERE子句中给出的条件表达式的元组
----按GROUP子句中指定列的值分组，同时提取满足HAVING子句中组条件表达式的那些组
----按SELECT子句中给出的列名或列表达式求值输出
----ORDER BY子句对输出的目标表进行排序。
```

### 1)等值判断

```sql
SELECT
    [ DISTINCT ] | 字段 | * [ AS ] [ 别名 ]...
FROM
    表名 [ 别名 ]
WHERE
    条件 字段1 = 值1, 字段2 = 值2...;
```

### 2)非等值判断
大于: `>`
小于: `<`
大于或等于: `>=`
小于或等于: `<=`
不等于: `!=, <>`
空值判断:`IS NULL, IS NOT NULL`
在...和...之间: `BETWEEN...AND...`
存在(相当于或): `ANY, SOME`
全部(相当于且): `ALL`

### 3)IN、NOT IN、EXISTS
`IN (LIST)`:存在于LIST
`NOT IN (LIST)`:不存在于LIST
`EXISTS (SUB_QUERY)`:在SUB_QUERY中

**例如：**

```sql
SELECT * FROM DEPT WHERE DEPTNO IN (SELECT DEPTNO FROM EMP WHERE SAL BETWEEN 2000 AND 3000);
SELECT * FROM DEPT d WHERE EXISTS (SELECT DEPTNO FROM EMP e WHERE SAL BETWEEN 2000 AND 3000 AND e.DEPTNO = d.DEPTNO);
```

### 4)模糊查询
`LIKE`：模糊查询关键字
`_`：通配符,代表一个模糊字符
`%`：通配符,代表0个或多个模糊字符
`ESCAPE`：转义(ESACPE '\')

### 5)排序
ORDER BY 字段名 ASC(升序)|DESC(降序)

```sql
SELECT * FROM EMP ORDER BY ASC|DESC;
```

**当group by出现时，select不能出现与分组无关的字段**

### 6)常用SQL函数
LOWER（字段名）：将字符串转换小写
UPPER（字段名）：将字符串转换大写
转换函数：
TOCHAR：转换成字符
TONUMBER：转换成数字
TODATE：转换成日期
DECODE：

```sql
DECODE (
    字段,
    值,    新值,
    值2, 新值2...
)
```

### 7)多行函数（组函数或聚合函数）
MAX(),MIN():求最大，最小
SUM(),AVG():求和，平均数
COUNT():计数

### 8)分组查询和伪列
将表中的记录进行分组。

```sql
SELECT  字段... FROM 表名 GROUP BY 字段 HAVING 限制(s);
```

### 9)ROWNUM和ROWID
ORACLE下的SELECT语句每隔结果集中都有一个伪字段存在，这个字段就是**ROWNUM**，用来表示每个记录的行号，行号从1开始，每行递增1。
**ROWID**是一种物理位置，用来记录唯一标识。
例如：

```sql
SELECT ROWNUM E.* FROM EMP E;
```

**常用作分页**，如：

```sql
SELECT ROWNUM, E.* FROM EMP E WHERE ROWNUM = 1;--第1条记录
SELECT ROWNUM, E.* FROM EMP E WHERE ROWNUM <= 9;--前9条记录
SELECT * FROM(SELECT ROWNUM RNO, E.* FROM EMP E) WHERE RNO >=6 AND RNO <= 10;--第6~10条记录(查询结果作为数据来源)
```

### 10)伪列
**伪列**即不存在的列，常用伪列进行统计

```sql
SELECT COUNT(1) FROM EMP GROUP BY DEPTNO；
```

**常见使用ROWID和ROWNUM**
***

#三、DML（Data Manipulation Language 数据操作语言
DML语句用于添加、删除、修改表中的数据，包含三条最常用的语句：INSERT、UPDATE和DELETE

## 1、INSERT
向指定表中添加记录

```sql
INSERT INTO 表名 
(    字段1,
    字段2...)--字段可省略
VALUES
(    值1,
    值2...);
```

## 2、DELETE
删除指定的记录

```sql
DELETE
FROM
    表名
WHERE
    条件 (s);
```

## 3、UPDATE;
修改指定元素的值

```sql
UPDATE 表名
SET 字段 = 新值,
 字段 = 新值...
WHERE
条件 (s);
```

***

# 四、DDL（Data Definition Language 数据定义语言）
DDL语句用于数据库对象的创建、修改和删除等常用语句，最典型的DDL语句就是创建表的语句、修改表结构的语句和删除整张表的语句。

## 1、表
常用的数据类型

|No.|字段类型|描述|
| :-------------: |:-------------|:-----|
|1|VARCHAR 、VARCHAR2 |表示字符串，长度不超过255|
|2|NUMBER    NUMBER(n)|表示整数，长度n，可使用INT|
|3|NUMBER(n,m)|表示小数，长度n，可使用float|
|4|DATE|标识日期类型|
|5|CLOB|大对象，表示大文本数据，一般存放4G文本|
|6|BLOB|大对象，表示二进制数据，最大存放4G的文本，例如：存放电影、歌曲、图片|

### 1.1创建表
语法规则：

```sql
    CREATE  TABLE  表名 (
        字段1  字段类型 [DEFAULT  默认值] , 
           字段2  字段类型 [DEFAULT  默认值] , 
           …
        字段n  字段类型 [DEFAULT  默认值] 
    );
```

复制表或表结构

```sql
CREATE TABLE 新表名 AS ( 子查询 );
```

### 1.2约束条件
在实际中，约束主要分为以下五种：
1) **PRIMARY KEY(主键约束)**: 主键表示一个唯一的标识，本身不能为空。  
        例如: 身份证号码就是唯一的，不可重复的，不可为空的。
2) **UNIQUE(唯一约束)**: 在一张表中只允许建立一个主键约束，而其它列如果不希望出现重复值的话，则就可以使用唯一约束。

```sql
ALTER TABLE 表 ADD CONSTRAINT 唯一约束名 UNIQUE (字段);
ALTER TABLE 表 ADD CONSTRAINT 唯一约束名 UNIQUE (字段)DISABLE;--不启用
```

3) **CHECK(检查约束)**: 检查一个列的内容是否合法。  
        例如：年龄，只能在 0 至 150之间。  
        例如：性别，只能是男，女。  
        
```sql
ALTER TABLE 表 ADD CONSTRAINT 检查约束名 CHECK (约束条件);
ALTER TABLE 表 ADD CONSTRAINT 检查约束名 CHECK (约束条件) DISABLE;--不启用
```

4) **NOT NULL(非空约束)**: 不允许字段值为 null 。
 
```sql
ALTER TABLE 表 MODIFY ( 字段 字段属性 NOT NULL  ) ;--设置非空约束
ALTER TABLE 表 MODIFY ( 字段 字段属性 NULL  ) ;--取消非空约束
```

5) **FOREIGN KEY(外键约束)**: 在两张表中进行约束操作。

```sql
ALTER TABLE 表2 ADD CONSTRAINT "外键名称" FOREIGN KEY (表2外键字段) REFERENCES 表1 (表1外键字段);
```

### 1.3修改表结构
1)增加字段

```sql
ALTER TABLE 表名 ADD (字段 字段类型);
```

2)删除字段

```sql
ALTER TABLE 表名 DROP (字段);
```

3)修改字段

```sql
ALTER TABLE 表名 MODIFY (字段 字段类型);
```

### 1.4删除表

```sql
DROP TABLE 表名;
```

## 2、索引
### 2.1索引的定义
**在ORACLE中索引是一种供服务器在表中快速查找一个行的数据库结构。**
### 2.2索引的特点
合理使用索引能够大大**提高数据库的运行效率**。
**主键会自动创建索引。**
### 2.3索引的创建

```sql
SET TIMING ON;--显示语句执行时间
CREATE INDEX 索引名 ON TABLE (字段1, 字段2...);--创建索引
DROP INDEX 索引名;--删除索引
```

## 3、视图
### 3.1视图的定义
视图是一个基于一个或多个表的**预定义查询**。试图也称之为虚表。

### 3.2试图的特点
视图并不存储数据，只是在查询视图时才访问基表。
**优点：**
>限制用户只能通过视图检索数据，对用户屏蔽基表  
可以将复杂的查询编写为视图，减少用户查询的复杂度  
限制某个视图只能访问基表中的部分数据，提高了安全性  
### 3.3视图的创建

```sql
GRANT 权限 TO 用户;--授权
REVOKE 权限 FROM 用户;--回收权限
CREATE [OR REPLACE] VIEW 视图名 AS SUBQUERY [WITH READ ONLY];--创建
SELECT * FROM 视图名;--查询
DROP VIEW 视图名;--删除视图
```

**注意:** 在创建视图时，若其后没有使用  `WITH READ ONLY`；
    则通过  `UPDATE 视图名 SET 字段 = 新值  WHERE 字段 = 值;` 修改记录值时，
    修改结果会直接映射到原表中！

## 4、序列
### 4.1序列的定义
oracle提供的用于产生一系列**唯一数字的数据库对象**。
### 4.2序列的特点
自动产生一个唯一的连续的数值
他是一个共享对象
将序列装入内存，可提供访问效率
### 4.3序列的作用
主要用于提供主键值。
### 4.4序列创建的格式

```sql
CREATE SEQUENCE 序列名 --创建序列名称
       [INCREMENT BY n] --增量 n ；若n是负数就递减 默认是1
       [START WITH n] --起始值,递增默认是minvalue 递减是maxvalue
       [{MAXVALUE n | NOMAXVALUE}] --最大值
       [{MINVALUE n | NOMINVALUE}] --最小值
       [{CYCLE | NOCYCLE}] --循环/不循环
       [{CACHE n | NOCACHE}]; --是否分配并存入到内存中
```

NEXTVAL 返回序列中下一个有效的值，任何用户都可以引用
CURRVAL 中存放序列的当前值
**注意:** NEXTVAL 应在 CURRVAL 之前指定 ，二者应同时有效

### 4.5操作案例: 
如何让某个表中的主键的值自动增加？

```sql
--删除序列
DROP SEQUENCE seq_AutoNum;
--删除表 
DROP TABLE tb_student;
--创建表
create table tb_student(
    id number(6) primary key ,
    name varchar2(30) 
);
--创建序列
Create sequence seq_AutoNum 
    Increment by 1  
    Start with 1
    Maxvalue 999999
    Minvalue 1
    Nocycle
    nocache ;
------------------------------------------------------------
--说明:
    --序列名: seq_AutoNum 
    --增量为: 1  
    --起始值: 1
    --最大值: 999999
    --最小值: 1
    --循环否: 否
    --并入内存否: 否
------------------------------------------------------------ 
insert into tb_student values(seq_AutoNum.nextval, 'Zhangsan');
commit;
select * from tb_student;
======================================================================================
--[了解] 
--1、查看当前用记下的所有表、视图、序列 及 解发器
    select table_name from user_tables;
    select view_name from user_views;
    select sequence_name from  user_sequences;
    select trigger_name from user_triggers;  //了解
--2、查看 SCOTT用户下的所有序列( 需要在sys状态下 )
    select * from dba_sequences where sequence_owner='SCOTT';
```

```
SEQUENCE_OWNER                 SEQUENCE_NAME                   MIN_VALUE  MAX_VALUE INCREMENT_BY C O CACHE_SIZE LAST_NUMBER
------------------------------ ------------------------------ ---------- ---------- ------------ - - ---------- -----------
SCOTT                          SEQ_CDPT                                1     999999            1 N N       0      3
SCOTT                          SEQ_NUM                                 1     999999            1 N N       0      4

======================================================================================
```

***

# 五、DCL（Data Control Language 数据控制语言）和事务控制语句TRANSACTION（事务处理）
## 1、DCL（Data Control Language 数据控制语言）
包括：GRANT（权限分配），REVOKE（权限回收），TRANSACTION（事务处理）。

```sql
GRANT 权限 TO 用户;--授权
REVOKE 权限 FROM 用户;--回收权限
```

## 2、事务控制语句TRANSACTION（事务处理）
包括：`COMMIT`（提交）、`ROLLBACK`（回滚）等常用语句。