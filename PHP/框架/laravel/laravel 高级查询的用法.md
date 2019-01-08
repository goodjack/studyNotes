### where 的闭包用法

```php
// 在闭包内关联其它表
where('id',function($query){
    // select 指明需要查询的它表数据
    // from 指定它表
    $query->select('id')->from('post'); // from 里的表名是数据库的表名，不是模型名
})
```

