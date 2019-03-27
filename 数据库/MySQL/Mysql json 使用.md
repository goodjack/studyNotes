#### 插入记录

```mysql
// 插入含有 json 数组的记录
insert into t_json(id,name,info) values (1,'name1',JSON_ARRAY(1,"abc",null,true,curtime()));
// 插入含有 json 对象的记录
insert into t_json(id,name,info) values (2,'name2','{"age":20,"time":now()}');
// 使用 json_object 函数创建一个 json 对象
insert into t_json(id,name,info) values (3,'name3',json_object('age',20,'time',now()));
```

#### 查询记录

```mysql
// 查询值
select sname,json_extract(info,'$.age') from t_json;
select sname,info->'$.age' from t_json;
// 查询key
select id,json_keys(info) from t_json;
```

#### 修改记录

```mysql
// 增加键
update t_json set info = json_set(info,'$.ip','192.168.0.1') where id = 2;
// 变更值
update t_json set info = json_set(info,'$.ip','192.168.1.1') where id = 2;
// 删除
update t_json set info = json_remove(info,'$.ip') where id = 2;
```

创建 json 值函数

```mysql
// json_array 生成 json 数组
select json_array(1,'abc',null,true,now());
// json_object 生成 json 对象
select json_object('age',20,'time',now());
// 将 json_quote json_val 用 “ 号括起来
select json_quote('[1,2,3]');
```

#### 搜索 json 值函数

```mysql
// json_contains 查看指定数据是否存在;
// json_contains(jsonObject,val,[path]) 查询 json 对象是否在指定 path 包含指定的数据，包含返回 1 否则返回 0，如果有参数为 null 或 path 不存在，则返回 null
set @j = json_object('a',1,'b',2,'c',json_object('d',4));
select json_contains(@j,'4','$.c.d'); 返回 1

// json_contains_path 指定路径是否存在
// json_contains_path(jsonObject,one|all,path,[path]...) 查询是否存在指定路径，存在返回 1 否则返回 0，如果有参数则为 null，one|all ，one 表示只要有一个存在即可，all 表示所有的都存在才行
select json_contains_path(@j,'one','$.a','$.e'); 返回 1
select json_contains_path(@j,'all','$.a','$.c.d'); 返回 1

// json_extract(jsonObject,path,[path]...) 从 json 对象抽取数据，如果有参数为 null 或 path 不存在，则返回 null，如果抽取多个 path，则返回的数据封闭在一个 json_array 里
set @j2 = '[10,20,[30,40]]';
select json_extract(@j2,'$[1]'); 20
select json_extract(@j2,'$[2][*]') [30,40]
// 如果是一个对象则可以 @j2->'$.a'

// json_keys 查找所有指定的键值
SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}'); ["a", "b"]
SELECT JSON_KEYS('{"a": 1, "b": {"c": 30}}', '$.b'); ["c"]


// json_search 查找所有指定值的位置
// json_search(jsonObject,one|all,search_str,[escape_char],[path]...) 查询包含指定字符串的 paths ，并作为一个 json array 返回
one|all one 表示查询带一个就返回，all 表示查询所有
search_str 要查询的字符串，可以使用 like 里的 ‘%’ 或 ‘_’ 匹配
path 在指定 path 下查

SET @j3 = '["abc", [{"k": "10"}, "def"], {"x":"abc"}, {"y":"bcd"}]';
SELECT JSON_SEARCH(@j3, 'one', 'abc'); -- "$[0]"
SELECT JSON_SEARCH(@j3, 'all', 'abc'); -- ["$[0]", "$[2].x"]
SELECT JSON_SEARCH(@j3, 'all', 'abc', NULL, '$[2]'); -- "$[2].x"
SELECT JSON_SEARCH(@j3, 'all', '10'); -- "$[1][0].k"
SELECT JSON_SEARCH(@j3, 'all', '%b%'); -- ["$[0]", "$[2].x", "$[3].y"]
SELECT JSON_SEARCH(@j3, 'all', '%b%', NULL, '$[2]'); -- "$[2].x"
```

#### 修改 json 值函数

```mysql
// json_array_append(jsonObject,path,val,[path,val]...) 指定位置追加数组元素
在指定 path 的 json_array 尾部追加 val，指定path是一个 json_object 则将其封装成一个 json_array 再追加
SET @j4 = '["a", ["b", "c"], "d"]';
-- SELECT JSON_ARRAY_APPEND(@j4, '$[1][0]', 3); -- ["a", [["b", 3], "c"], "d"]
SET @j5 = '{"a": 1, "b": [2, 3], "c": 4}';
SELECT JSON_ARRAY_APPEND(@j5, '$.b', 'x'); -- {"a": 1, "b": [2, 3, "x"], "c": 4} 
SELECT JSON_ARRAY_APPEND(@j5, '$.c', 'y'); -- {"a": 1, "b": [2, 3], "c": [4, "y"]}
SELECT JSON_ARRAY_APPEND(@j5, '$', 'z'); -- [{"a": 1, "b": [2, 3], "c": 4}, "z"]

// json_array_insert(jsonObject,path,val,[path,val]...) 在指定位置插入数组元素
在 path 指定的 json array 元素插入 val，原位置以及右的元素顺次右移，如果 path 指定的数据非 json array 元素，则略过此 val，指定的元素下标超过 json array 的长度，则插入尾部
SET @j6 = '["a", {"b": [1, 2]}, [3, 4]]';
SELECT JSON_ARRAY_INSERT(@j6, '$[1]', 'x'); -- ["a", "x", {"b": [1, 2]}, [3, 4]]
SELECT JSON_ARRAY_INSERT(@j6, '$[100]', 'x'); -- ["a", {"b": [1, 2]}, [3, 4], "x"]
SELECT JSON_ARRAY_INSERT(@j6, '$[1].b[0]', 'x'); -- ["a", {"b": ["x", 1, 2]}, [3, 4]]
SELECT JSON_ARRAY_INSERT(@j6, '$[0]', 'x', '$[3][1]', 'y'); -- ["x", "a", {"b": [1, 2]}, [3, "y", 4]]

// json_insert(jsonObject,path,val,[path,val]...) 指定位置插入 在指定 path 下插入数据，如果 path 已存在，则忽略此 val
SET @j7 = '{ "a": 1, "b": [2, 3]}';
SELECT JSON_INSERT(@j7, '$.a', 10, '$.c', '[true, false]'); -- {"a": 1, "b": [2, 3], "c": "[true, false]"}


// json_replace(jsonObject,path,val,[path,val]...) 指定位置替换，如果某个路径不存在则略过（存在才替换）
SELECT JSON_REPLACE(@j7, '$.a', 10, '$.c', '[true, false]'); -- {"a": 10, "b": [2, 3]}

// json_set(jsonObject,path,val,[path,val]...) 指定位置设置，设置指定路径的数据（不管是否存在）
SELECT JSON_SET(@j7, '$.a', 10, '$.c', '[true, false]'); -- {"a": 10, "b": [2, 3], "c": "[true, false]"}

// json_remove(jsonObject,path,[path]...) 指定位置移除，如果某个路基不存在则略过此路径
SET @j8 = '["a", ["b", "c"], "d"]';
SELECT JSON_REMOVE(@j8, '$[1]'); -- ["a", "d"]

// json_merge_patch 合并且去重
select json_merge_patch('{"name":"x"}','{"id":47}') {"id":47,"name":"x"}
select json_merge_patch('[1,2]','{"id":47}')
{"id":47}
select json_merge_patch('{"a":1,"b":2}','{"a":3,"c":4}');
{"a":3,"b":2,"c":4}
select json_merge_patch('{"a":1,"b":2}','{"b":null}')
{"a":1}

// json_merge_preserve 合并保留重复的值
```

#### 返回 json 属性的函数

```mysql
// json_length(jsonObjec,[path]) 长度
SELECT JSON_LENGTH('[1, 2, {"a": 3}]'); -- 3
SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}'); -- 2
SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30}}', '$.b'); -- 1

// json_type 类型
select JSON_TYPE('[1,2]'); -- ARRAY

// json_valid 是否是有效 json
SELECT JSON_VALID('{"a": 1}'); -- 1
```

