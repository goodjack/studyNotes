# MySQL语句

### SQL分类：

DDL：数据定义语言（CREATE,ALTER,DROP,DECLARE)

DML：数据操作语言（SELECT,DELETE,UPDATE,INSERT）

DCL：数据控制语言（GRANT,REVOKE,COMMIT,ROLLBACK）

#### DLL语句：

创建数据库并指定字符集：`create database dbname default charset utf8mb4 collate utf8mb4_unicode_ci`

删除数据库：`drop database dbname`

备份数据库：`mysqldump -u root dbname>filename`

备份表：`mysqldump -u root dbname tablename>filename`

导入：`load data infile 'path/file' into table tablename`

导出：`select col1,col2 into outfile 'path/file' from tablename`注意：文件路径下不能有同名

恢复：`source 'filepath'`和`mysql -u root databasename < sqlfile`

### 外键

使用条件：

1. 两个表必须是 InnoDB 表，MyISAM 表暂时不支持外键
2. 外键列必须建立索引
3. 外键关系的两个表的列必须是数据类型相似（可以相互转换类型的列）如：int 和 tinyint 

创建外键，该语法可以在 CREATE TABLE 和 ALTER TABLE 时使用，如不指定 CONSTRAINT symbol，MySQL会自动生成一个名字：

```mysql
[CONSTRAINT symbol] FOREIGN KEY [id] (index_col_name, ...)
REFERENCES tbl_name (index_col_name, ...)
[ON DELETE {RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT}]
[ON UPDATE {RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT}]
```

ON DELETE 、 ON UPDATE 表示事件触发限制，可选参数：

- RESTRICT：如果想删除的主表，它的下面有对应从表的记录，此主表将无法删除
- CASCADE：如果主表的记录删掉，则从表中相关联的记录都将被删掉
- SET NUll ：将外键设置为空
- SET DEFAULT：设置为空
- NO ACTION：无动作，默认的 

删除外键：`ALTER TABLE user DROP FOREIGN KEY user_id`