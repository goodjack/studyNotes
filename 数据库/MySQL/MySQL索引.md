# MySQL索引

#### 索引的存储分类

- **B-Tree索引：**最常见的索引类型，大部分引擎都支持B树索引
- **HASH索引：**只有Memory引擎支持，使用场景简单
- **R-Tree索引（空间索引）：**空间索引是MyISAM的一种特殊索引类型，主要用于地理空间数据类型
- **Full-Text（全文索引）：**全文索引也是MyISAM的一种特殊索引类型，主要用于全文索引，InnoDB从MYSQL5.6版本提供对全文索引的支持

**MySQL目前不支持函数索引**，但是能对列的前面某一部分进行索引，例如标题title字段，可以只取title的前10个字符进行索引，这个特性可以大大缩小索引文件的大小，但索引前缀也有缺点，在排序`Order by`和分组`Group by`操作的时候无法使用，在设计表结构的时候可以对文本列根据此特性进行灵活设计。

语法：`create index idx_title on film (title(10))`

| 索引          | MyISAM引擎 | InnoDB引擎   | Memory引擎 |
| ------------- | ---------- | ------------ | ---------- |
| B-Tree索引    | 支持       | 支持         | 支持       |
| HASH索引      | 不支持     | 不支持       | 支持       |
| R-Tree索引    | 支持       | 不支持       | 不支持     |
| Full-Text索引 | 支持       | 支持（5.6+） | 不支持     |

**B-TREE索引类型**

- PRIMARY KEY （主键索引）：`alter table 'table_name' add primary key ('column')`
- UNIQUE（唯一索引）：`alter table 'table_name' add unique ('column')`
- INDEX（普通索引）：`alter table 'table_name' add index index_name ('column')`
- FULLTEXT（全文索引）：`alter table 'table_name' add fulltext ('column')`
- 组合索引：`alter table 'table_name' add index index_name ('column','column','column')`

**索引区别**

- 普通索引：最基本的索引，没有任何限制
- 唯一索引：与普通索引类似，不同就是，索引列的值必须唯一，但允许有空值
- 主键索引：特殊的唯一索引，不允许有空值
- 全文索引：仅可用于 MyISAM 表，针对较大的数据，生成全文索引很耗时耗空间
- 组合索引：为了更多的提高 MySQL 效率可建立组合索引，遵循最左前缀原则

<span style="color:red;">注：不能用CREATE INDEX 语句创建PRIMARY KEY索引</span>



#### 索引的设置语法

**设置索引**

1. `ALTER TABLE`来创建普通索引、UNIQUE索引或PRIMARY KEY索引

```mysql
ALTER TABLE table_name ADD INDEX index_name (column_list);		 # 创建普通索引
ALTER TABLE table_name ADD UNIQUE (column_list);				# 创建UNIQUE唯一索引
ALTER TABLE table_name ADD PRIMARY KEY (column_list);			#  创建PRIMARY KEY主键索引
```

2. `CREATE INDEX`可对表增加普通索引或UNIQUE索引

```mysql
CREATE INDEX index_name ON table_name (column_list);		# 普通索引
CREATE UNIQUE INDEX index_name ON table_name (column_list);  # UNIQUE唯一索引
```

**删除索引**

可利用`ALTER TABLE`或`DROP INDEX`语句来删除索引。类似于`CREATE INDEX`语句，`DROP INDEX`可以在`ALTER TABLE`内部作为一条语句处理，语法如下：

```mysql
DROP INDEX index_name ON table_name;
ALTER TABLE table_name DROP INDEX index_name;
ALTER TABLE table_name DROP PRIMARY KEY;
```

其中，前两条语句是等价的，删除掉table_name中的索引index_name。

第3条语句只在删除PRIMARY KEY 索引时使用，因为一个表只可能有一个PRIMARY KEY索引，因此不需要指定索引名。如果没有创建PRIMARY KEY索引，但表具有一个或多个UNIQUE索引，则MySQL将删除第一个UNIQUE索引。

**查看索引**

```mysql
show index from tablename;
show keys from tablename;
```

- **Table：**表的名称
- **Non_unique：**如果索引不能包括重复词，则为0.如果可以则为1
- **Key_name：**索引的名称
- **Seq_in_index：**索引中的序列号，从1开始
- **Column_name：**列名称
- **Collation：**列以什么方式存储在索引中。在MySQL中有值A（升序）或NULL（无分类）
- **Cardinality：**索引中唯一值的数目估计值，通过运行`ANALYZE TABLE`或`myisamchk -a`可以更新。基数根据被存储为整数的统计数据来计数，所以即使对于小型表，该值也没有必要时精确的。基数越大，当进行联合是，MySQL使用该索引的机会越大。
- **Sub_part：**如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL
- **Packed：**指示关键字如何被压缩，如果没有被压缩，则为NULL
- **Null：**如果列含有Null，则含有YES，如果没有，则该列含有NO
- **Index_type：**索引方法（BTREE,FULLTEXT,HASH,RTREE）

#### 索引选择性

**索引选择原则**

1. 较频繁的作为查询条件的字段应该创建索引
2. 唯一性太差的字段不适合单独创建索引，即使频繁作为查询条件
3. 更新非常频繁的字段不适合创建索引

通常是通过比较同一时间段内被更新的次数和利用该字段作为条件的查询次数来判断的，如果通过该字段的查询不是很多，可能几个小时或更长才会执行一次，更新反而比查询更频繁，那这样的字段肯定不适合创建索引。反之，如果我们通过该字段的查询比较频繁，但更新并不是特别多，比如查询几十次或更多才可能会产生一次更新。

4. 不会出现在WHERE子句中的字段不该创建索引

**索引选择原则细述**

- 性能优化过程中，考虑使用索引的主要有两种类型：**在WHERE子句中出现的列，在join子句中出现的列，而不是在SELECT关键字后选择列表的列**
- 索引列的基数越大，索引的效果越好
- 使用短索引
- 利用最左前缀

**索引选择注意事项**

索引加快查询速度的同时，也会消耗存储空间，并且索引会加重插入、删除和修改记录时的负担，另外MySQL在运行时也要消耗资源维护索引，因此索引不是越多越好。

以下情况不建议使用索引：

1. 表记录比较少，可以以**2000作为分界线**
2. 索引的选择性较低。所谓索引的选择性（Selectivity），是指不重复的索引值（也叫基数，Cardinality）与表记录数（#T）的比值：

`Index Selectivity = Cardinality / #T`

显然选择性的取值范围为（0,1），选择性越高的索引价值越大，这是由B+Tree的性质决定的。如果title字段经常被单独查询，是否需要建立索引，看下它的选择性：

`SELECT COUNT(DISTINCT(title))/COUNT(*) AS Selectivity FROM employees.titles;`

3. MySQL只对以下操作符才使用索引：`<,<=,=,>,>=,between,in`以及某些时候的like（不以通配符%或_开头的情形）

#### MySQL存储引擎MyISAM与InnoDB的区别

|                      | MyISAM                                                       | InnoDB                                                       |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 存储结构             | 每张表存放三个文件：1.frm - 表格定义 2. MYD(MYData) - 数据文件 3. MYI(MYIndex) - 索引文件 | 所有的表都保存在同一数据文件中（也可能是多个文件或者是独立的表空间文件），InnoDB表的大小只受限于操作系统文件的大小，一般为2GB |
| 存储空间             | MyISAM可被压缩，存储空间较小                                 | InnoDB的表需要更多的内存和存储，它会在主内存中建立其专用的缓冲池，用于高速缓冲数据和索引 |
| 可移植性、备份及恢复 | 由于MyISAM的数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作 | 免费的方案可以是拷贝数据文件、备份binlog，或者用mysqldump，在数据量达到几十G的时候就相对痛苦 |
| 事务安全             | 不支持，每次查询具有原子性                                   | 支持具有事务、回滚和崩溃修复能力（crash recovery capabilities）的事务安全（transaction-safe（ACID compliant）型表 |
| AUTO_INCREMENT       | MyISAM表可以和其他字段一起建立联合索引                       | InnoDB中必须包含只有该字段的索引                             |
| SELECT               | MyISAM更优                                                   |                                                              |
| INSERT               |                                                              | InnoDB更优                                                   |
| UPDATE               |                                                              | InnoDB更优                                                   |
| DELETE               |                                                              | InnoDB更优 它不会重新建立表，而是一行一行的删除              |
| COUNT without WHERE  | MyISAM更优，因为MyISAM保存了表的具体行数                     | InnoDB没有保存表的具体行数，需要逐行扫描统计，就很慢         |
| COUNT with WHERE     | 一样                                                         | 一样，InnoDB也会锁表                                         |
| 锁                   | 只支持表锁                                                   | 支持表锁、行锁。行锁大幅度提高了多用户并发操作的性能，但InnoDB的行锁，只是在WHERE的主键是有效的，非主键的WHERE都会锁全表的 |
| 外键                 | 不支持                                                       | 支持                                                         |
| FULLTEXT全文索引     | 支持                                                         | 5.6+支持可以通过使用Sphinx从InnoDB中获得全文索引             |

InnoDB的设计目标是处理大容量数据库系统，它的CPU利用率是其它基于磁盘的关系数据库引擎所不能比的

InnoDB对高并发的处理比MyISAM高效，同时结合memcache也可以缓存select来减少select查询，从而提高整体性能