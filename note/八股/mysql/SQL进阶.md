#### 存储引擎

##### MySQL体系结构

![image-20221018150813791](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221018150813791.png)

<font color='blue'>连接层</font>

​		最上层是一些客户端和链接服务，主要完成一些类似连接处理、授权认证、及相关的安全方案。服务器也会为安全接入的每个客户验证它所具有的操作权限。

<font color='blue'>服务层</font>

​		第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。

<font color='blue'>引擎层</font>

​		存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。

<font color='blue'>存储层</font>

​		主要是将数据存储在文件系统之上，并完成与存储引擎的交互。

##### 存储引擎简介

###### 1.在创建表时，指定存储引擎

```sql
CREATE TABLE 表名(
	字段1 字段1类型[COMMENT 字段1注释],
    ...
    字段n 字段n类型[COMMENT 字段n注释]
)ENGINE=INNODB[COMMENT 表注释];
```

###### 2.查看当前数据库支持的存储引擎

```sql
SHOW ENGINES;
```

##### 存储引擎特点

###### InnoDB

​		InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在My5QL 5.5之后，InnoDB是默认的MySQL存储引擎。

<font color='blue'>特点</font>

DML操作遵循ACID模型，支持事务；

行级锁，提高并发防问性能；

支持外键FOREIGN KEY约束，保证数据的完整性和正确性；

<font color='blue'>文件</font>

xxx.ibd: xxx代表的是表名，innoDB引擎的每张表都会对应这样一个表空间文件，存储该表的表结构(frm、 sdi)、数据和索引。
参数: innodb_file_per_table

![image-20221018155659317](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221018155659317.png)

###### MyISAM

​		MyISAM是MySQL早期的默认存储引擎。

<font color='blue'>特点</font>

​		不支持事务，不支持外键

​		支持表锁，不支持行锁

​		访问速度快

<font color='blue'>文件</font>

​		XXX.sdi：存储表结构信息

​		XXX.MYD：存储数据

​		XXX.MYI：存储索引

###### Memory

​		Memory引擎的表数据时存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用。

<font color='blue'>特点</font>

​		内存存放

​		hash索引（默认）

<font color='blue'>文件</font>

​		XXX.sdi：存储表结构信息

###### 总结

| <font color='red'>特点</font> | <font color='red'>InnoDB</font> | <font color='red'>MyISAM</font> | <font color='red'>Memory</font> |
| ----------------------------- | ------------------------------- | ------------------------------- | ------------------------------- |
| **存储限制**                  | 64TB                            | 有                              | 有                              |
| **事务安全**                  | 支持                            | -                               | -                               |
| **锁机制**                    | 行锁                            | 表锁                            | 表锁                            |
| **B+tree索引**                | 支持                            | 支持                            | 支持                            |
| **Hash索引**                  | -                               | -                               | 支持                            |
| **全文索引**                  | 支持（5.6版本之后）             | 支持                            | -                               |
| **空间使用**                  | 高                              | 低                              | N/A                             |
| **内存使用**                  | 高                              | 低                              | 中等                            |
| **批量插入速度**              | 低                              | 高                              | 高                              |
| **支持外键**                  | 支持                            | -                               | -                               |

###### 存储引擎的选择

​		在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

InnoDB：是Mysql的默认存储引擎，支持事务、外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么InnoDB存储引擎是比较合适的选择。

MylSAM：如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的。

MEMORY：将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。MEMORY的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性。

#### 索引结构

​		索引(index）是帮助MysQL高效获取数据的数据结构(有序)。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

| <font color='red'>优势</font>                                | <font color='red'>劣势</font>                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 提高数据检索的效率，降低数据库的IO成本                       | 索引列也是要占用空间的                                       |
| 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗。 | 索引大大提高了查询效率，同时却也降低更新表的速度，如对表进行INSERT、UPDATE、DELETE时，效率降低。 |

##### 索引结构

MySQL的索引是在存储引擎层实现的，不同的存储引擎有不同的结构，主要包含以下几种：

| <font color='red'>索引结构</font> | <font color='red'>描述</font>                                |
| --------------------------------- | ------------------------------------------------------------ |
| **B+Tree索引**                    | **最常见的索引类型，大部分引擎都支持B+树索引**               |
| Hash索引                          | 底层数据结构是用哈希表实现的，只有精确匹配索引列的查询才有效，不支持范围查询 |
| R-Tree(空间索引)                  | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
| Full-text(全文索引)               | 是一种通过建立倒排索引，快速匹配文档的方式。类似Luncene，Solr，ES |

![image-20221019164318707](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019164318707.png)

<font color='red'>我们平常所说的索引，如果没有特别指明，都是指B+树结构组织的索引。</font>

##### B+Tree结构

二叉树缺点：顺序插入时，会形成一个链表，查询性能大大降低。大数据量情况下，层级较深，检索速度慢。

红黑树：大数据量情况下，层级较深，检索速度慢。

###### B-Tree（多路平衡查找树）

![image-20221019165235013](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019165235013.png)

###### B+Tree

![image-20221019165522527](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019165522527.png)

相对于B-Tree区别：

1.所有的数据都会出现在叶子节点

2.叶子节点形成一个单向链表

###### B+Tree索引结构

MySQL索引数据结构对经典的B+Tree进行了优化。在原B+Tree的基础上，增加一个指向相邻叶子节点的键表指针，就形成了带有顺序指针的B+Tree，提高区间访问的性能。

![image-20221019165855133](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019165855133.png)

##### Hash索引结构

​		哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。

​		如果两个(或多个)键值，映射到一个相同的槽位上，他们就产生了hash冲突〈也称为hash碰撞)，可以通过链表来解决。

![image-20221019170121582](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019170121582.png)



Hash索引特点

1. Hash索引只能用于对等比较(=，in)，不支持范围查询(between，>，<，...)
2. 无法利用索引完成排序操作
3. 查询效率高，通常只需要一次检索就可以了，效率通常要高于B+tree索引



​		在MysQL中，支持hash索引的是Memory引擎，而innoDB中具有自适应hash功能，hash索引是存储引擎根据B+Tree索引在指定条件下自动构建的。

![image-20221019170608110](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019170608110.png)

#### 索引分类

| <font color='red'>分类</font> | <font color='red'>含义</font>                        | <font color='red'>特点</font> | <font color='red'>关键字</font> |
| ----------------------------- | ---------------------------------------------------- | ----------------------------- | ------------------------------- |
| **主键索引**                  | 针对表中主键创建的索引                               | 默认自动创建，只能有一个      | PRIMARY                         |
| **唯一索引**                  | 避免同一个表中某数据列中的值重复                     | 可以有多个                    | UNIQUE                          |
| **常规索引**                  | 快速定位特定数据                                     | 可以有多个                    |                                 |
| **全文索引**                  | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个                    | FULLTEXT                        |

在lnnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种:

| <font color='red'>分类</font> | <font color='red'>含义</font>                              | <font color='red'>特点</font> |
| ----------------------------- | ---------------------------------------------------------- | ----------------------------- |
| **聚集索引**(Clustered lndex) | 将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据 | 必须有，而且只有一个          |
| **二级索引**(Secondary Index) | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 | 可以存在多个                  |

聚集索引选取规则：

如果存在主键，主键索引就是聚集索引。

如果不存在主键，将使用第一个唯一(UNIQUE）索引作为聚集索引。

如果表没有主键，或没有合适的唯一索引，则nnoDB会自动生成一个rowid作为隐藏的聚集索引。

![image-20221019171810117](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019171810117.png)

根据name查数据的话就是从二级索引找到主键，然后主键索引找到对应数据，这个过程称为回表查询

#### 索引语法

##### 创建索引

```sql
CREATE [ UNIQUE |FULLTEXT ] INDEX index_name ON table_name ( index_col_nam...);
```

##### 查看索引

```sql
SHOW INDEX FROM table_naem;
```

##### 删除索引

```sql
DROP INDEX index_name ON table_naem;
```

![image-20221019173228929](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221019173228929.png)

#### SQL性能分析

##### SQL执行频率

​		MySQL客户端连接成功后，通过show [sessionlglobal]status命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次：

```sql
SHOW GLOBAL STATUS LIKE 'Com_______';
```

##### 慢查询日志

​		慢查询日志记录了所有执行时间超过指定参数(long_query_time，单位:秒，默认10秒〉的所有SQL语句的日志。

​		MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件(/etc/my.cnf）中配置如下信息:

```sql
#开启MySQL慢日志查询开关
slow_query_log=1
#设置慢日志的时间为2秒，5QL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time=2
```

​		配置完毕之后，通过以下指令重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息/varlib/mysql/localhost-slow.log。

##### profile详情

​		show profiles能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过have_profiling参数，能够看到当前MySQL是否支持

profile操作：

```sql
SELECT @@have profiling;
```

默认profiling是关闭的，可以通过set语句在session/global级别开启profiling:

```sql
SET profiling = l;
```

执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时:

```sql
#查看每一条SQL的耗时基本情况
show profiles;

#查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query query_id;

#查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query query_id;
```

##### explain执行计划

​	EXPLAIN或者DESC命令获取MySQL如何执行SELECT语句的信息，包括在SELECT语句执行过程中表如何连接和连接的顺序。

```sql
#直接在select语句之前加上关键字explain/desc
EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件;
```

![image-20221020003844289](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020003844289.png)

explain执行计划各字段含义：

###### Id

​		select查询的序列号，表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下；id不同，值越大，越先执行)。

###### select_type

​		表示SELECT的类型，常见的取值有SIMPLE（简单表，即不使用表连接或者子查询)、PRIMARY（主查询，即外层的查询)、UNION (UNION中的第二个或者后面的查询语句）、SUBQUERY (SELECT/WHERE之后包含了子查询）等

###### type

​		表示连接类型，性能由好到差的连接类型为NULL、system、const、eq_ref、ref、range、index、all 。

###### possible_key

显示可能应用在这张表土的索引，一个或多个。

###### Key

实际使用的索引，如果为NJLL，则没有使用索引。

###### Key_len

​		表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好。

###### rows

​		MySQL认为必须要执行查询的行数，在innodb引擎的表中，是一个估计值，可能并不总是准确的。

###### filtered

​		表示返回结果的行数占需读取行数的百分比, filtered 的值越大越好。

#### 索引使用

##### 验证索引效率

在未建立索引之前，执行如下SQL语句，查看SQL的耗时。

```sql
SELECT*FROM tb_sku WHERE Sn = "1000Q0003145007';
```

针对字段创建索引

```sql
create index idx_sku_sn on tb_sku(sn);
```

##### 索引使用原则

###### 最左前缀法则

​		如果索引了多列(联合索引)，要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。

如果跳跃某一列，索引将部分失效（后面的字段索引失效）

![image-20221020162101864](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020162101864.png)

###### 范围查询

​		联合索引中，出现范围查询(>，<)，范围查询右侧的列索引失效。

![image-20221020162753890](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020162753890.png)

###### 索引列运算

​		不要在索引列上进行运算操作，索引将失效。

![image-20221020163102744](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020163102744.png)

###### 字符串不加引号

​		字符串类型字段使用时，不加引号，索引将失效。

![image-20221020163704786](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020163704786.png)

###### 模糊查询

​		如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效。

![image-20221020163850146](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020163850146.png)

###### or连接的条件

​		用or分割开的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。

![image-20221020164040313](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020164040313.png)

​		由于age没有索引，所以即使id、phone有索引，索引也会失效。所以需要针对于age也要建立索引。

###### 数据分布影响

​		如果MysQL评估使用索引比全表更慢，则不使用索引。

![image-20221020164504502](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020164504502.png)

###### SQL提示

​		SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

use index：用哪个索引

```sql
explain select * from tb_user use index(idx_user_pro) where profession= '软件工程';
```

ignore index：不用哪个索引

```sql
explain select * from tb_user ignore index(idx_user_pro) where profession= "软件工程';
```

force index：必须用哪个索引

```sql
explain select * from tb_user force index(idx_user_pro) where profession= '软件工程';
```

###### 覆盖索引

​		尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到)，减少select *。

![image-20221020165410537](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020165410537.png)

![image-20221020165947416](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020165947416.png)

###### 前缀索引

​		当字段类型为字符串(varchar,text等)时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘lO，影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

```sql
create index idx_xxoxx on table_name(column(n));
```

前缀长度

​		可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高,唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。

```sql
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(ernail, 1,5)) / count(*) from tb_user;
```

###### 单列索引与联合索引

单列索引：即一个索引只包含单个列。

联合索引：即一个索引只包含多个列。

在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。

![image-20221020184803957](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020184803957.png)

联合索引情况:

![image-20221020185002512](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221020185002512.png)

#### 索引设计原则

1.针对于数据量较大，且查询比较频繁的表建立索引。

2.针对于常作为查询条件(where)、排序（order by)、分组(group by)操作的字段建立索引。
论

3.尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。

4.如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。

5.尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。

6.要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。

7.如果索引列不能存储NULL值，请在创建表时使用NOTNULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。

#### SQL优化

##### 插入数据

批量插入

```sql
insert into tb_test values(1 , 'Tom").(2,'Cat').(3,'Jerry');
```

手动提交事务

```sql
start transaction;
insert into tb_test values(1,'Torn'"),(2,'Cat'"),(3,"Jerry');
insert into tb_test values(4,'Ton').(5,'Cat').(6.'Jerry');
nsert into tb_test values(7 ,'Ton').(8,'Cat').(9,'Jerry');
commit;

```

主键顺序插入

```sql
主键乱序插入:8 l 9 21 88 2 4 15 89 5 7 3
主键顺序插入: 1 2 3 4 5 7 8 9 15 21 88 89
```

大批量插入数据

​		如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令进行插入。操作如下:

![image-20221021162630006](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221021162630006.png)

```sql
客户端连接服务端时，加上参数--local-infile
mysql --local-infile -u root -p
#!设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;
#执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table tb_user fieldsterminated by ',' lines terminated by '\n';
```

<font color='red'>主键顺序插入性能高于乱序插入</font>

##### 主键优化

**数据组织方式**

​		在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为<font color='red'>索引组织表</font>(index organized table lOT)。

**页分裂**

​		页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据多大，会行溢出)，根据主键排列。

**页合并**

​		当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged)为删除并且它的空间变得允许被其他记录声明使用。

​		当页中删除的记录达到 MERGE_THRESHOLD(默认为页的50%)，InnoDB会开始寻找最靠近的页（前或后）看看是否可以将两个页合并以优化空间使用。

![image-20221021164116547](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221021164116547.png)

**主键设计原则**

​		满足业务需求的情况下，尽量降低主键的长度。

​		插入数据时，尽量选择顺序插入，选择使用AUTO_INCREMENT自增主键。

​		尽量不要使用UUID做主键或者是其他自然主键，如身份证号。

​		业务操作时，避免对主键的修改。

##### order by优化

①、Using filesort :通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort bufer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫FileSort排序。

②、Using index:通过有序索引顺序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高。

```sql
#没有创建索引时，根据age, phone进行排序
explain select id,age,phone from tb_user order by age , phone;
#创建索引
create index idx_user_age_phone_aa on tb_user(age,phone);
#创建索引后，根据age, phone进行升序排序
explain select id,age,phone from tb_user order by age , phone;
#创建索引后，根据age, phone进行降序排序
explain select id,age,phone from tb_user order by age desc , phone desc;
```

```sql
#根据age, phone进行降序一个升序，一个降序
explain select id,age,phone from tb_user order by age asc , phone desc;
#创建索引
create index idx_user_age _phone_ad on tb_user(age asc ,phone desc);
#根据age, phone进行降序一个升序，一个降序
explain select id,age,phone from tb_user order by age asc , phone desc;

```

<font color='red'>总结</font>

​		跟据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则。

​		尽量使用覆盖索引。

​		多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）。

​		如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小sort_buffer_size(默认256Kk)。

##### group by优化

```sql
#删除掉目前的联合索引 idx_user_pro_age_sta
drop index idx_user_pro_age_sta on tb_user;
#执行分组操作，根据profession字段分组
explain select profession , count(*) from tb_user group by profession ;
#创建索引
Create index idx_user_pro_age_sta on tb_user(profession , age , status);
#*执行分组操作，根据profession字段分组
explain select profession , count(*) from tb_user group by profession;
#执行分组操作，根据profession字段分组
explain select profession , count(*) from tb_user group by profession , age;

```

​		在分组操作时，可以通过索引来提高效率。

​		分组操作时，索引的使用也是满足最左前缀法则的。

##### limit优化

​		一个常见又非常头疼的问题就是limit 2000000,10，此时需要MySQL排序前2000010记录，仅仅返回2000000 - 2000010的记录，其他记录丢弃，查询排序的代价非常大。

<font color='red'>优化思路：</font>一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化。

```sql
explain select * from tob_sku t , (select id from tb_sku order by id limit 2000000,10) a where t.id = a.id;
```

##### count优化

```sql
explain select count(*) from tb_user ;
```

​		MyISAM引擎把一个表的总行数存在了磁盘上，因此执行count(*)的时候会直接返回这个数，效率很高。

​		InnoDB引擎就麻烦了，它执行count(*)的时候，需要把数据一行一行地从引擎里面读出来，然后累积计数。

<font color='red'>优化思路：自己计数。</font>

count的几种用法

​		count()是一个聚合函数，对于返回的结果集，一行行地判断，如果count 函数的参数不是NULL，累计值就加1，否则不加，最后返回累计值。

用法:count(* )、 count（主键)、count(字段)、count ( 1)

count（主键)

​		InnoDB引擎会遍历整张表，把每一行的主键id 值都取出来，返回给服务层。服务层拿到主键后，直接按行进行累加(主键不可能为null)。

count（字段)

​		没有not null约束: InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为null，不为null，计数累加。

​		有not null约束: lInnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接按行进行累加。

count ( 1)

​		InnoDB引擎遍历整张表，但不取值。服务层对于返回的每一行，放一个数字“1”进去，直接按行进行累加。

count(* )

​		InnoDB引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加。

<font color='red'>按照效率排序的话，count(字段)<count(主键id)<count(1) ≈count(* )，所以尽量使用count(*)。</font>

##### update优化

​		<font color='red'>InnoDB的行锁是针对索引加的锁，不是针对记录加的锁,并且该索引不能失效，否则会从行锁升级为表锁。</font>

#### 视图/存储过程/触发器

##### 视图

​		视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。

​		通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。

<font color='blue'>创建</font>

```sql
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)]AS SELECT语句「WITH [ CASCADED | LOCAL ] CHECK OPTION]
```

<font color='blue'>查询</font>

```sql
查看创建视图语句:SHOW CREATE VIEW 视图名称;
查看视图数据:SELECT *FROM 视图名称...... ;
```

<font color='blue'>修改</font>

```sql
方式一:CREATE [OR REPLACE] VIEW视图名称[(列名列表)]AS SELECT语句〔WITH[ CASCADED | LOCAL ] CHECK OPTION]
方式二:ALTER VIEW视图名称[(列名列表)]AS SELECT语句[WITH[ CASCADED | LOCAL] CHECK OPTION]

```

<font color='blue'>删除</font>

```sql
DROP VIEW[IF EXISTS]视图名称[视图名称]...
```

###### 视图的检查选项

​		当使用WITHCHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如插入，更新，删除，以使其符合视图的定义。MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项:CASCADED和LOCAL，默认值为CASCADED。

###### 视图的更新

​		要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一项，则该视图不可更新：

1.聚合函数或窗口函数（SUM()、MIN()、MAX()、COUNT()等)

2.DISTINCT

3.GROUP BY

4.HAVING

5.UNION 或者UNION ALL

###### 作用

**简单：**视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。

**安全：**数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据

**数据独立：**视图可帮助用户屏蔽真实表结构变化带来的影响。



##### 存储过程

​		存储过程是事先经过编译并存储在数据库中的一段SQL语句的集合，调用存储过程可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。

​		存储过程思想上很简单，就是数据库SQL语言层面的代码封装与重用。

特点：

封装，复用

可以接收参数，也可以返回数据

减少网络交互，效率提升

###### 基本语法

<font color='blue'>创建</font>

```sql
CREATE PROCEDURE存储过程名称(参数列表])
BEGIN
-- SQL语句
END;

```

<font color='blue'>调用</font>

```sql
CALL 名称([参数);
```

<font color='blue'>查看</font>

```sql
SELECT*FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA= 'xxx';--查询指定数据库的存储过程及状态信息
SHOW CREATE PROCEDURE存储过程名称;--查询某个存储过程的定义

```

<font color='blue'>删除</font>

```sql
DROP PROCEDURE [ IF EXISTS]存储过程名称;
```

<font color='red'>注意：在命令行中，执行创建存储过程的SQL时，需要通过关键字delimiter 指定SQL语句的结束符。</font>

###### 变量

<font color='purple'>系统变量</font>

​		系统变量是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量(GLOBAL)、会话变量(SESSION)。

<font color='blue'>查看系统变量</font>

```sql
SHOW [ SESSION | GLOBAL ]VARIABLES;   --查看所有系统变量
SHOW [ SESSION | GLOBAL] VARIABLES LIKE '...' ;--可以通过LIKE模糊匹配方式查找变量
SELECT @@[SESSION | GLOBAL]系统变量名;  --查看指定变量的值
```

<font color='blue'>设置系统变量</font>

```sql
SET [ SESSION |GLOBAL]系统变量名=值;

SET @@[SESSION |GLOBAL]系统变量名=值;
```

<font color='red'>注意：</font>

<font color='red'>如果没有指定SESSION/GLOBAL，默认是SESSION，会话变量。</font>

<font color='red'>mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在/etc/my.cnf 中配置。</font>



<font color='purple'>用户定义变量</font>

​		用户定义变量是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用“@变量名”使用就可以。其作用域为当前连接。

<font color='blue'>赋值</font>

```sql
SET @var_name = expr [, @var_name = expr] ...;
SET @var_name:= expr [, @var_name:= expr] ... ;
```

```sql
SELECT @var_name := expr [ @var_name := expr] ...;
SELECT 字段名 INTO @var_name FROM 表名;
```

<font color='blue'>使用</font>

```sql
SELECT @var_name;
```

<font color='red'>注意：</font>

<font color='red'>用户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL。</font>



<font color='purple'>局部变量</font>

​		局部变量是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ...END块。

<font color='blue'>声明</font>

```sql
DECLARE 变量名 变量类型[DEFAULT ...];
```

变量类型就是数据库字段类型：INT、BIGINT、CHAR、VARCHAR、DATE、TIME等。

<font color='blue'>赋值</font>

```sql
SET 变量名=值;
SET 变量名:=值;
SELECT 字段名 INTO 变量名 FROM 表名..;
```

###### if

<font color='blue'>语法</font>

```sql
IF 条件1 THEN
		....
ELSEIF 条件2 THEN			--可选
		....
ELSE					--可选
		....
END IF;
```

###### 参数

![image-20221024203610088](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221024203610088.png)

<font color='blue'>用法：</font>

```sql
CREATE PROCEDURE 存储过程名称([IN/OUT/INOUT 参数名 参数类型])
BEGIN
	--SQL语句
END;
```

###### case

<font color='blue'>语法一</font>

```sql
CASE case_value
	WHEN when_value1 THEN statement_list1
	[ WHEN when_value2 THEN statement_list 2] ...
	[ ELSE statement_list ]
END CASE;
```

<font color='blue'>语法二</font>

```sql
CASE
	WHEN search_condition1 THEN statement_list1
	[WHEN search_condition2 THEN statement_list2] ..
	[ELSE statement_list]
END CASE;
```

###### while

while循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。具体语法为:

```sql
#先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
WHILE 条件 DO
		SQL逻辑...
END WHILE;
```

###### repeat

repeat是有条件的循环控制语句,当满足条件的时候退出循环。具体语法为:

```sql
#先执行一次逻辑，然后判定逻辑是否满足，如果满足，则退出。如果不满足，则继续下一次循环
REPEAT
	SQL逻辑...
	UNTIL 条件
END REPEAT;
```

###### loop

Loop实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的死循环。LooP可以配合一下两个语句使用：

 LEAVE ：配合循环使用，退出循环。

 ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。

```sql
[begin_label:] LOOP
		SQL逻辑...
END LOOP [end_label];
```

```sql
LEAVE label;	--退出指定标记的循环体
ITERATE label;	--直接进入下一次循环
```

###### 游标

​		游标（CURSOR）是用来存储查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集进行循环的处理。游标的使用包括游标的声明、OPEN、FETCH 和CLOSE，其语法分别如下。

声明游标

```sql
DECLARE 游标名称 CURSOR FOR 查询语句;
```

打开游标

```sql
OPEN 游标名称;
```

获取游标记录

```sql
FETCH 游标名称 INTO变量[，变量];
```

关闭游标

```sql
CLOSE 游标名称;
```

![image-20221024210844094](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221024210844094.png)

###### 条件处理程序

​		条件处理程序(Handler)可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。具体语法为：

```sql
DECLARE handler_action HANDLER FOR condition_value [, condition_value]... statement;

handler_action
		CONTINUE:继续执行当前程序
		EXIT:终止执行当前程序
		
condition_value
		SQLSTATE sqlstate_value:状态码，如02000
		SQLWARNING:所有以01开头的SQLSTATE代码的简写
		NOT FOUND:所有以02开头的SQLSTATE代码的简写
SQLEXCEPTION:所有没有被SQLWARNING或NOT FOUND捕获的SQLSTATE代码的简写
```



##### 存储函数

​	存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的。具体语法如下:

```sql
CREATE FUNCTION存储函数名称([参数列表])
RETURNS type [characteristic ...]
BEGIN
	--SQL语句
	RETURN ...;
END ;
characteristic说明:
	DETERMINISTIC:相同的输入参数总是产生相同的结果
	NO SQL:不包含SQL语句。
	READS SQL DATA:包含读取数据的语句，但不包含写入数据的语句。
type是返回参数的类型，int char等等
```



##### 触发器

​		触发器是与表有关的数据库对象，指在insert/update/delete 之前或之后，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作。

​		使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发。

![image-20221025142805883](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025142805883.png)

###### 语法

<font color='blue'>创建</font>

```sql
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE
ON tbl_name FOR EACH ROW --行级触发器
BEGIN
	trigger_stmt;
END;

```

<font color='blue'>查看</font>

```sql
SHOW TRIGGERS ;
```

<font color='blue'>删除</font>

```sql
DROP TRIGGER [schema_name.]trigger_name;
--如果没有指定schema_name，默认为当前数据库。
```

#### 锁

​		锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

##### 全局锁

​		全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被阻塞。

​		其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。

![image-20221025150325515](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025150325515.png)

<font color='blue'>特点</font>

数据库中加全局锁，是一个比较重的操作，存在以下问题：

1.如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。

2.如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志 (binlog)，会导致主从延迟。



​		在InnoDB引擎中，我们可以在备份时加上参数--single-transaction参数来完成不加锁的一致性数据备份。

```sql
mysqldump --single-transaction -uroot-p123456 itcast > itcast.sql
```



##### 表级锁

​		表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中。

对于表级锁，主要分为以下三类：表锁、元数据锁（meta data lock ,MDL）、意向锁

###### 表锁

语法：

1.加锁：lock tables 表名... read/write。

2.释放锁：unlock tables /客户端断开连接。

<font color='purple'>1.表共享读锁（ read lock)：</font>

![image-20221025151825670](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025151825670.png)

<font color='purple'>2.表独占写锁(write lock)：</font>

![image-20221025151845396](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025151845396.png)

​		<font color='red'>读锁不会阻塞其他客户端的读，但是会阻塞写。写锁既会阻塞其他客户端的读，又会阻塞其他客户端的写。</font>



###### 元数据锁(meta data lock ,MDL)

​		MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。<font color='red'>为了避免DML与DDL冲突，保证读写的正确性。</font>

​		在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享);当对表结构进行变更操作的时候，加MDL写锁(排他)。

![image-20221025153202530](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025153202530.png)

查看元数据锁

```sql
select object_type,object_schema,object_name,lock_type,lock duration from performance_schema.metadata_locks;
```



###### 意向锁

1.意向共享锁（IS)：由语句select ... lock in share mode添加。

2.意向排他锁(IX)：由insert、update.delete、select ... for update添加。



1.意向共享锁（IS)︰与表锁共享锁( read）兼容，与表锁排它锁( write）互斥。

2.意向排他锁（IX)︰与表锁共享锁（read)及排它锁(write)都互斥。意向锁之间不会互斥。

可以通过以下SQL，查看意向锁及行锁的加锁情况:

```sql
select object_schema,object_name,index_name,lock_type,ock_mode.lock_data from performance_schema.data_locks;
```



##### 行级锁

​		行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB存储引擎中。

​		InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁。对于行级锁，主要分为以下三类：

1. 行锁〈Record Lock)：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。
2. 间隙锁（Gap Lock)：锁定索引记录间隙（不含该记录)，确保索引记录间隙不变，防止其他事务在这个间隙进行insert,产生幻读。在RR隔离级别下都支持。
3. 临键锁（Next-Key Lock)：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持。

###### 行锁

lnnoDB实现了以下两种类型的行锁:
1.共享锁(S)∶允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。

2.排他锁（X)︰允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。

![image-20221025201138965](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025201138965.png)

![image-20221025201259911](C:\Users\WZ\AppData\Roaming\Typora\typora-user-images\image-20221025201259911.png)

​		默认情况下，InnoDB在REPEATABLE READ事务隔离级别运行，InnoDB使用next-key锁进行搜索和索引扫描，以防止幻读。

1.针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。

2.InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁。

可以通过以下SQL，查看意向锁及行锁的加锁情况：

```sql
select object_schema,object_name,index_name,Jock_type,lock_mode,lock data from performance_schema.data_locks;
```

###### 间隙锁&临键锁

​		默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用next-key锁进行搜索和索引扫描，以防止幻读。

1．索引上的等值查询(唯一索引)，给不存在的记录加锁时,优化为间隙锁。

2．索引上的等值查询(普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock退化为间隙锁。

3．索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

<font color='red'>注意：间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。</font>

