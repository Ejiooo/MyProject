#### DDL（定义数据库对象（数据库，表，字段））

##### DDL-数据库操作

###### 1.查询

查询所有数据库

```sql
SHOW DATABASES;
```

查询当前数据库

```sql
SELECT DATABASE();
```

###### 2.创建

```sql
CREATE DATABASE [IF NOT EXISTS]数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
```

[]为可选，第一个是没有该数据库才创建，使用的时候没有[]

###### 3.删除

```sql
DROP DATABASE [IF EXISTS]数据库名;
```

###### 4.使用

```sql
USE 数据库名;
```



##### (一)DDL-表操作-查询

###### 1.查询当前数据库所有表

```sql
SHOW TABLES;
```

###### 2.查询表结构

```sql
DESC 表名;
```

###### 3.查询指定表的建表语句

```sql
SHOW CREATE TABLE 表名;
```

##### (二)DDL-表操作-创建

```sql
CREATE TABLE 表名(
	字段1 字段1类型[COMMENT 字段1注释],
    字段2 字段2类型[COMMENT 字段2注释],
    字段3 字段3类型[COMMENT 字段3注释],
    ...
    字段n 字段n类型[COMMENT 字段n注释]
)[COMMENT 表注释];
```

<font color='red'>注意：[...]为可选参数，最后一个字段后面没有逗号</font>

##### (三)DDL-表结构-修改

###### 1.添加字段

```sql
ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];
```

###### 2.修改数据类型

```sql
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);
```

###### 3.修改字段名和字段类型

```sql
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];
```

###### 4.删除字段

```sql
ALTER TABLE 表名 DROP 字段名;
```

###### 5.修改表名

```sql
ALTER TABLE 表名 RENAME TO 新表名;
```

###### 5.删除表

```sql
DROP TABLE [IF EXISTS] 表名;
```

###### 6.删除指定表，并重新创建该表

```sql
TRUNCATE TABLE 表名;
```



#### DML（增删改）

##### DML-添加数据

###### 1.给指定字段添加数据

```sql
INSERT INTO 表名(字段1,字段2,...) VALUES(值1,值2,...);
```

###### 2.给全部字段添加数据

```sql
INSERT INTO 表名 VALUES(值1,值2,...);
```

###### 3.批量添加数据

```sql
INSERT INTO 表名(字段1,字段2,...) VALUES(值1,值2,...),(值1,值2,...),(值1,值2,...);
```

```sql
INSERT INTO 表名 VALUES(值1,值2,...),(值1,值2,...),(值1,值2,...);
```

<font color='red'>注意：</font>

<font color='red'>1.插入数据时，指定的字段顺序需要与值得顺序是一一对应的</font>。

<font color='red'>2.字符串和日期类型数据应该包含在引号中。</font>

<font color='red'>3.插入的数据大小，应该在字段的规定范围内。</font>

##### DML-修改数据

```sql
UPDATE 表名 SET 字段名1=值1,字段名2=值2,... [WHERE 条件];
```

##### DML-删除数据

```sql
DELETE FROM 表名 [WHERE 条件];
```

<font color='red'>注意：</font>

<font color='red'>1.DELETE语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据</font>。

<font color='red'>2.DELETE语句不能删除某一字段的值(可以使用UPDATE)。</font>

#### DQL(查询)

##### DQL语法

```sql
SELECT
	字段列表	
FROM
	表名列表
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

##### DQL-基本查询

###### 1.查询多个字段

```sql
SELECT 字段1,字段2,字段3... FROM 表名;
```

```sql
SELECT * FROM 表名;
```

###### 2.设置别名

```sql
SELECT 字段1[AS 别名1],字段2[AS 别名2] ... FROM 表名;
```

###### 3.去除重复记录

```sql
SELECT DISTINCT 字段列表 FROM 表名;
```

##### DQL-条件查询

###### 1.语法

```sql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

###### 2.条件

| <font color='red'>比较运算符</font> | <font color='red'>功能</font>                |
| ----------------------------------- | -------------------------------------------- |
| >                                   | 大于                                         |
| >=                                  | 大于等于                                     |
| <                                   | 小于                                         |
| <=                                  | 小于等于                                     |
| =                                   | 等于                                         |
| <>或!=                              | 不等于                                       |
| BETWEEN...AND...                    | 在某个范围之内（含最小、最大值）             |
| IN(...)                             | 在in之后的列表中的值，多选一                 |
| LIKE 占位符                         | 模糊匹配( _ 匹配单个字符， % 匹配任意个字符) |
| IS NULL                             | 是NULL                                       |

| <font color='red'>逻辑运算符</font> | <font color='red'>功能</font> |
| ----------------------------------- | ----------------------------- |
| AND 或 &&                           | 并且(多个条件同时成立)        |
| OR 或 \|\|                          | 或者(多个条件任意一个成立)    |
| NOT 或 !                            | 非,不是                       |

##### DQL-聚合函数

###### 1.介绍

​		将一列数据作为一个整体，进行纵向计算。

###### 2.常见的聚合函数

| <font color='red'>函数</font> | <font color='red'>功能</font> |
| ----------------------------- | ----------------------------- |
| count                         | 统计数量                      |
| max                           | 最大值                        |
| min                           | 最小值                        |
| avg                           | 平均值                        |
| sum                           | 求和                          |

###### 3.语法

```sql
SELECT 聚合函数(字段列表) FROM 表名;
```

<font color='red'>注意：null值不参与所有聚合函数运算。</font>

##### DQL-分组查询

###### 1.语法

```sql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
```

###### 2.where与having区别

​		执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤

​		判断条件不同：where不能对聚合函数进行判断，而having可以。



<font color='red'>注意：</font>

​		<font color='red'>执行顺序：where > 聚合函数 > having</font>

​		<font color='red'>分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。</font>

##### DQL-排序查询

###### 1.语法

```sql
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2;
```

###### 2.排序方式

​		ASC：升序(默认值)

​		DESC：降序



<font color='red'>注意：</font>

​		<font color='red'>如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序。</font>

##### DQL-分页查询

###### 1.语法

```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引,查询记录数;
```

<font color='red'>注意：</font>

​		<font color='red'>起始索引从0开始，起始索引 = (查询页码-1) * 每页显示记录数。</font>

​		<font color='red'>分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。</font>

​		<font color='red'>如果查询的是第一页数据，起始索引可以省略，直接简写为limit 10。</font>



#### DCL（权限）

##### DCL-管理用户

###### 1.查询用户

```sql
USE mysql;
SELECT * FROM user;
```

###### 2.创建用户

```sql
CREATE USER '用户名' @ '主机名' IDENTIFIED BY '密码';
```

###### 3.修改用户密码

```sql
ALTER USER '用户名' @ '主机名' IDENTIFIED WITH mysql_native_passwod BY '新密码';
```

###### 4.删除用户

```sql
DROP USER '用户名' @ '主机名';
```

<font color='red'>注意：</font>

​		<font color='red'>主机名可以使用%通配。</font>

​		<font color='red'>这类SQL开发人员操作的比较少，主要是DBA（数据库管理员）使用。</font>

##### DCL-权限控制

​		MySQL中定义了很多种权限，但是常用的就以下几种：

| <font color='red'>权限</font> | <font color='red'>说明</font> |
| ----------------------------- | ----------------------------- |
| ALL,ALL PRIVILEGES            | 所有权限                      |
| SELECT                        | 查询数据                      |
| INSERT                        | 插入数据                      |
| UPDATE                        | 修改数据                      |
| DELETE                        | 删除数据                      |
| ALTER                         | 修改表                        |
| DROP                          | 删除数据库/表/视图            |
| CREATE                        | 创建数据库/表                 |

###### 1.查询权限

```sql
SHOW GRANTS FOR '用户名' @ '主机名';
```

###### 2.授予权限

```sql
GRANT 权限列表 ON 数据库名.表名 TO '用户名' @ '主机名';
```

###### 3.撤销权限

```sql
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名' @ '主机名';
```

<font color='red'>注意：</font>

​		<font color='red'>多个权限之间，使用逗号分隔。</font>

​		<font color='red'>授权时，数据库名和表名可以使用*进行通配，代表所有。</font>

#### 函数

##### 字符串函数

| <font color='red'>函数</font> | <font color='red'>功能</font>                             |
| ----------------------------- | --------------------------------------------------------- |
| CONCAT(S1,S2,...Sn)           | 字符串拼接，将S1,S2,...Sn拼接成一个字符串                 |
| LOWER(str)                    | 将字符串str全部转为小写                                   |
| UPPER(str)                    | 将字符串str全部转为大写                                   |
| LPAD(str,n,pad)               | 左填充，用字符串pad队str的左边进行填充，达到n个字符串长度 |
| RPAD(str,n,pad)               | 右填充，用字符串pad队str的右边进行填充，达到n个字符串长度 |
| TRIM(str)                     | 去掉字符串头部和尾部的空格                                |
| SUBSTRING(str,start,len)      | 返回从字符串str从start位置起的len个长度的字符串           |

```sql
SELECT 函数(参数);
```

-- 案例:  由于业务需求变更，企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0。比如： 1号员工的工号应该为00001。

```sql
update emp set workno = lpad(workno, 5, '0');
```

##### 数值函数

| <font color='red'>函数</font> | <font color='red'>功能</font>      |
| ----------------------------- | ---------------------------------- |
| CEIL(x)                       | 向上取整                           |
| FLOOR(x)                      | 向下取整                           |
| MOD(x,y)                      | 返回x/y的模                        |
| RAND()                        | 返回0~1内的随机数                  |
| ROUND(x,y)                    | 求参数x的四舍五入的值，保留y位小数 |

-- 案例: 通过数据库的函数，生成一个六位数的随机验证码。

```sql
select lpad(round(rand()*1000000 , 0), 6, '0');
```

##### 日期函数

| <font color='red'>函数</font>     | <font color='red'>功能</font>                     |
| --------------------------------- | ------------------------------------------------- |
| CURDATE()                         | 返回当前日期                                      |
| CURTIME()                         | 返回当前时间                                      |
| NOW()                             | 返回当前日期和时间                                |
| YEAR(date)                        | 获取指定date的年份                                |
| MONTH(date)                       | 获取指定date的月份                                |
| DAY(date)                         | 获取指定date的日期                                |
| DATE_ADD(date,INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1,date2)             | 返回起始时间date1和结束时间date2之间的天数        |

-- date_add

```sql
select date_add(now(), INTERVAL 70 YEAR );
```

-- 案例: 查询所有员工的入职天数，并根据入职天数倒序排序。

```sql
select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by entrydays desc;
```

##### 流程函数

可以在SQL语句中实现条件筛选，从而提高语句效率

| <font color='red'>函数</font>                             | <font color='red'>功能</font>                          |
| --------------------------------------------------------- | ------------------------------------------------------ |
| IF(value,t,f)                                             | 如果value为true，则返回t，否则返回f                    |
| IFNULL(value1,value2)                                     | 如果value1不为空，返回value1，否则返回value2           |
| CASE WHEN [val1] THEN [res1] ...ELSE [default] END        | 如果val1为true，返回res1,...否则返回default默认值      |
| CASE [expr] WHEN [val1] THEN [res1] ...ELSE [default] END | 如果expr值等于val1，返回res1，...否则返回default默认值 |

-- case when then else end
-- 需求: 查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市 , 其他 ----> 二线城市)

```sql
select
    name,
    ( case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end ) as '工作地址'
from emp;
```

-- 案例: 统计班级各个学员的成绩，展示的规则如下：
-- >= 85，展示优秀
-- >= 60，展示及格
-- 否则，展示不及格

```sql
create table score(
    id int comment 'ID',
    name varchar(20) comment '姓名',
    math int comment '数学',
    english int comment '英语',
    chinese int comment '语文'
) comment '学员成绩表';
insert into score(id, name, math, english, chinese) VALUES (1, 'Tom', 67, 88, 95 ), (2, 'Rose' , 23, 66, 90),(3, 'Jack', 56, 98, 76);

```

```sql
select
    id,
    name,
    (case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end ) '数学',
    (case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格' end ) '英语',
    (case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格' end ) '语文'
from score;

```

#### 约束

##### 概述

1.概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

2.目的：保证数据库中数据的正确、有效性和完整性。

3.分类：

| <font color='red'>约束</font> | <font color='red'>描述</font>                            | <font color='red'>关键字</font> |
| ----------------------------- | -------------------------------------------------------- | ------------------------------- |
| 非空约束                      | 限制该字段的数据不能为null                               | NOT NULL                        |
| 唯一约束                      | 保证该字段的所有数据都是唯一，不重复的                   | UNIQUE                          |
| 主键约束                      | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY                     |
| 默认约束                      | 保存数据时，如果未指定该字段的值，则采取默认值           | DEFAULT                         |
| 检查约束(8.0.16版本之后)      | 保证字段值满足某一条件                                   | CHECK                           |
| 外键约束                      | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY                     |

  <font color='red'>注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。</font>

##### 演示

![image-20221015074205154](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221015074205154.png)

```sql
create table user(
    id int primary key auto_increment comment '主键',
    name varchar(10) not null unique comment '姓名',
    age int check ( age > 0 && age <= 120 ) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
) comment '用户表';
```

auto_increment为自增

##### 外键约束

###### 添加外键

```sql
CREATE TABLE 表名(
	字段名 数据类型,
    ...
    [CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);
```

```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名);
```

###### 删除外键

```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```

###### 删除/更新行为

| <font color='red'>行为</font> | <font color='red'>说明</font>                                |
| ----------------------------- | ------------------------------------------------------------ |
| NO ACTION                     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。(与RESTRICT一致) |
| RESTRICT                      | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。(与NO ACTION一致) |
| CASCADE                       | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| SET NULL                      | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null(这就要求该外键允许取null)。 |
| SET DEFAULT                   | 父表有变更时，子表将外键列设置成一个默认的值(Innodb不支持)   |

```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名) ON UPDATE CASCADE ON DELETE CASCADE;
```

#### 多表查询

##### 多表关系

###### 一对多（多对一）

案例:部门与员工的关系

关系:一个部门对应多个员工，一个员工对应一个部门

实现:<font color='red'>在多的一方建立外键，指向一的一方的主键</font>

![image-20221015160225602](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221015160225602.png)

###### 多对多

案例:学生与课程的关系

关系:一个学生可以选修多门课程，一门课程也可以供多个学生选择

实现:<font color='red'>建立第三张中间表，中间表至少包含两个外键，分别关联两方主键</font>

![image-20221015160545930](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221015160545930.png)

###### 一对一

案例:用户与用户详情的关系

关系:一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率

实现:<font color='red'>在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)</font>

![image-20221015160934635](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221015160934635.png)

##### 多表查询概述



概述:指从多张表中查询数据

笛卡尔积:笛卡尔乘积是指在数学中，两个集合A集合和B集合的所有组合情况。(在多表查询时，需要消除无效的笛卡尔积)

###### 分类

连接查询

内连接：相当于查询A、B交集部分数据
外连接:
		左外连接：查询<font color='red'>左表</font>所有数据，以及两张表交集部分数据
		右外连接：查询<font color='red'>右表</font>所有数据，以及两张表交集部分数据
自连接：当前表与自身的连接查询，自连接必须使用表别名

子查询

##### 内连接

###### 1.隐式内连接

```sql
SELECT 字段列表 FROM 表1,表2 WHERE 条件...;
```

###### 2.显式内连接

```sql
SELECT 字段列表 FROM 表1 [INNER] JOIN 表2 ON 连接条件...;
```

##### 外连接

###### 左外连接

```sql
SELECT 字段列表 FROM 表1 LEFT [OUTER] JOIN 表2 ON 条件...;
```

###### 右外连接

```sql
SELECT 字段列表 FROM 表1 RIGHT [OUTER] JOIN 表2 ON 条件...;
```

##### 自连接

```sql
SELECT 字段列表 FROM 表A 别名A JOIN 表B 别名B ON 条件...;
```

<font color='red'>自连接查询，可以是内连接查询，也可以是外连接查询。</font>

###### 联合查询-union,union all

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

```sql
SELECT 字段列表 FROM 表A ...
UNION[ALL]
SELECT 字段列表 FROM 表B ...;
```

<font color='red'>对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。</font>

<font color='red'>union all会将全部的数据直接合并在一起，union会对合并之后的数据去重。</font>

##### 子查询

概念：SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询

```sql
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```

<font color='red'>子查询外部的语句可以是INSERT/ UPDATE/DELETE / SELECT的任何一个。</font>

根据子查询结果不同，分为:
		标量子查询(子查询结果为单个值)
		列子查询(子查询结果为一列)
		行子查询(子查询结果为一行)
		表子查询(子查询结果为多行多列)

###### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询成为标量子查询。

常用的操作符：=  <>  >  >=  <  <=

根据销售部门ID，查询员工信息

```sql
select * from emp where dept_id = (select id from dept where nane = '销售部');
```

###### 列子查询

子查询返回的结果是一列（可以是多行)，这种子查询称为列子查询。

常用的操作符：IN , NOT IN , ANY , SOME , ALL

![image-20221015165803555](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221015165803555.png)

###### 行子查询

子查询返回的结果是一行（可以是多列)，这种子查询称为行子查询。

常用的操作符：= , <> , IN , NOT IN

查询与“张无忌”的薪资以及直属领导相同的员工信息;

```sql
select * from emp where 
(salary ,managerid) = (select salary， managerid from emp where name = ‘张无忌' );

```

###### 表子查询

子查询返回的结果是多行多列,这种子查询称为表子查询。

常用的操作符：IN

查询与“鹿杖客”，“宋远桥”的职位和薪资相同的员工信息

```sql
select * from emp 
where (job , salary) in 
( select job，salary frnom emp whene name = ‘鹿杖客’ or name =‘宋远桥’);
```



#### 事务

##### 事务操作

###### 查看/设置事务提交方式

```sql
SELECT @@autocommit;
SET @@autocommit=0;
```

###### 开启事务

```sql
START TRANSACTION 或 BEGIN;
```

###### 提交事务

```sql
COMMIT;
```

###### 回滚事务

```sql
ROLLBACK;
```

##### 事务四大特性

原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。

一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。

隔离性（lsolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。

持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。



##### 并发事务问题

| <font color='red'>问题</font> | <font color='red'>描述</font>                                |
| ----------------------------- | ------------------------------------------------------------ |
| 脏读                          | 一个事务读到另外一个事务还没有提交的数据                     |
| 不可重复读                    | 一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读 |
| 幻读                          | 一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了“幻影”。 |

##### 事务的隔离级别

| <font color='red'>隔离级别</font> | <font color='red'>脏读</font> | <font color='red'>不可重复读</font> | <font color='red'>幻读</font> |
| --------------------------------- | ----------------------------- | ----------------------------------- | ----------------------------- |
| Read uncommitted                  | √                             | √                                   | √                             |
| Read committed                    | ×                             | √                                   | √                             |
| Repeatable Read(默认)             | ×                             | ×                                   | √                             |
| Serializable                      | ×                             | ×                                   | ×                             |

<font color='red'>注意：事务的隔离级别越高，数据越安全，但是性能越低。</font>

###### 查看事务的隔离级别

```sql
SELECT @@TRANSACTION_ISOLATION;
```

###### 设置事务的隔离级别

```sql
SET[SESSIDN|GLOBAL ] TRANSACTION ISOLATION LEVEL{READ UNCOMMITED |READ COMMITED │REPEATABLE READ| SERIALZABLE)
```

