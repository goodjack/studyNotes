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