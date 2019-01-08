# MySQL大批量数据插入

提高数据插入效率：

1. 批量插入数据的效率比单数据行插入的效率高

2. 插入无索引的数据表比插入有索引的数据表快一些

3. 将所有查询语句写入事务

4. 利用Load Data导入数据

5. 可以用队列分批处理

   

- load data（1）

- 优化SQL语句：将SQL语句进行拼接，批量插入（2）

```mysql
这样插入如果字符串太长，则需要配置下MySQL，在MySQL命令行运行：
set gloabl max_allowed_packet = 2*1024*1024*10
$sql = "insert into scan_card_picture(uuid,account_id) values";
for($i=0;$i<2000000;$i++){
    $str = mt_rand();
    $sql .="('{$str}','A7kVzZYK2EyAXm2jIVVpF0ls4M2LS00000001044'),";
}
$sql = rtrim($sql,',');
var_dump($sql);
```

- 事务提交，批量插入数据库（每隔10w条提交）（3）

```php
$connect_mysql->query('BEGIN');
$params = array('value'=>'50');
for($i=0;$i<2000000;$i++){
    $connect_mysql->insert($params);
    if($i%100000==0){
        $connect_mysql->query('COMMIT');
        $connect_mysql->query('BEGIN');
    }
}
$connect_mysql->query('COMMIT');
```

- for效率最慢，插入数据少时可以使用