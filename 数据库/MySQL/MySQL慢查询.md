# MySQL慢查询

#### 参数

`slow_query_log`：是否开启慢查询日志，1开启，0关闭

`log-slow-queries`：旧版本（5.6以下版本）MySQL数据库慢查询日志存储路径，可以不设置该参数，系统会默认给一个缺省的文件`host_name-slow.log`

`slow-query-log-file`：新版（5.6及以上版本）MySQL数据库慢查询日志存储路径。可以不设置该参数，系统则会默认给一个缺省的文件`host_name-slow.log`

`long_query_time`：慢查询阈值，当查询时间多余设定的阈值是，记录日志

`log_queries_not_using_indexes`：未使用索引的查询也被记录到慢查询日志中（可选）

`log_output`：日志存储方式。`log_output='FILE'表示将日志存入文件，默认值'FILE',值为'TABLE'表示将日志存入数据库

mysqldumpslow工具，mysql提供的日志分析工具

[mysql explain详解 ](https://www.awaimai.com/2141.html)