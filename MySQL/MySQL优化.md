### MySQL 优化

- 不查询不需要的列
- 不要在多表关联返回全部的列
- 不要 `select *`
- 不要重复查询，应当写入缓存
- 尽量使用关联查询来替代子查询
- 尽量使用索引优化。如果不使用索引，MySQL 则使用临时表或者文件排序。如果不关心结果集的顺序，可以使用 `order by null` 禁用文件排序
- 优化分页查询，最简单的就是利用覆盖索引扫描，而不是查询所有列
- 应尽量避免在 where 子句中使用 `!=` 或 `<>` 操作符，否则引擎将放弃使用索引而进行全表扫描
- 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引
- 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如：

`select * from user where name is null`

- 尽量不要使用前缀 %

`select * from user where name like '%a'`

- 应尽量避免在 where 子句中对字段进行表达式操作
- 应尽量避免在 where 子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描
- 大表使用 exists ，小表使用 in

### btree 索引

B-TREE 索引适合全键值、键值范围、前缀查找

全值匹配，是所有的列进行匹配

匹配最左前缀。如 `a=1&b=2` 那么会用到 a 的索引

匹配列前缀。如 `abc abcd %abc`

匹配范围，如 `in(3,5)`

#### 限制

- 如果不是左前缀开始查找，无法使用索引 如 `%aa`
- 不能跳过索引列
- 查询中有列范围查询，则其右边的的所有列都无法使用索引优化：如 有 3 个索引，`last_name、first_name、dob` 

`where last_name = 'Smith'AND first_name like 'J%' AND dob = '1976-12-23'` ，因为 like 是范围条件，这里会使用两个索引

#### 索引优点

1. 减少服务器扫描表的次数
2. 避免排序和临时表
3. 将随机 IO 变成顺序 IO

#### 高性能索引策略

- 使用独立的列，而不是计算的列

`where num+1 =10 // bad`

`where num = 9 // good`

- 使用前缀索引
- 多列索引，应该保证左序优先
- 覆盖索引
- 选择合适的索引顺序

不考虑排序和分组的情况，在选择性最高的列上，放索引

- 使用索引扫描来排序

MySQL 有两种方式生成有序的结果，一种排序操作，一种按索引顺序扫描

如果 explain 处理的 type 列的值是 index，则说明 MySQL 使用了索引

只用当索引的列顺序和 order by 子句的顺序一致的时候，并且所有顺序都一致的时候，MySQL 才能使用索引进行排序

### 不使用索引的情况

- 查询使用了两种排序方向

`select * from user where login_time > '2018-01-01' order by id desc,username asc`

- order by 中含有一个没有索引的列

`select * from user where name = '11' order by age desc // age 没有索引`

- where 和 order by 无法形成最左前缀
- 索引列的第一列是范围条件
- 在索引列上有多个等于条件，这也是一种范围，无法使用索引
