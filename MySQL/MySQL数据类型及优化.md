选择正确的数据类型：

1. 确定合适的大类型：数字、字符串、时间、二进制；

2. 确定具体的类型：有无符号、取值范围、变长定长等；

在数据类型设置方面，尽量用更小的数据类型，因为通常有更好的性能，花费更少的硬件资源，并且尽量把字段定义为`NOT NULL`，避免使用`NULL`

**数值类型**

> TINYINT：小整数值
>
> SMALLINT：大整数值
>
> MEDIUMINT：大整数值
>
> INT或INTEGER：大整数值
>
> BIGINT：极大整数值
>
> FLOAT：单精度浮点数值
>
> DOUBLE：双精度浮点数值
>
> DECIMAL：小数值

**优化建议**

1. 如果整形数据没有负数，如ID号，建议指定为`UNSIGNED`无符号类型，容量可扩大一倍
2. 建议使用`TINYINT`代替`ENUM`、`BITENUM`、`SET`
3. 避免使用整数的显示宽度，也就是说，不要用`INT(10)`类似的方法指定字段显示宽度，直接用`INT`
4. 用`INT UNSIGNED`来存储IPv4地址，用`VARBINARY`来存储IPv6地址，存储之前需要用PHP函数转换
5. `DECIMAL`最适合保存准确度要求高，而且用于计算的数据，比如价格。在使用`DECIMAL`类型的时候，注意长度设置
6. 建议使用整形类型来运算和存储实数，方法是，实数乘以相应的倍数后再操作
7. 整数通常是最佳的数据类型，因为它速度快，并且能使用`AUTO_INCERMENT`



**日期和时间**

> DATA：日期值
>
> TIME：时间值或持续时间
>
> YEAR：年份值
>
> DATETIME：混合日期和时间值
>
> TIMESTAMP混合日期和时间值，时间戳

**优化建议**

1. MySQL能存储的最小时间粒度为秒
2. 建议用`DATE`数据类型来保存日期，MySQL中默认日期格式`yyyy-mm-dd`
3. 用MySQL的内建类型`DATE`、`TIME`、`DATETIME`来存储时间，而不是使用字符串
4. 当数据格式为`TIMESTAMP`和`DATETIME`时，可以用`CURRENT_TIMESTAMP`作为默认（MySQL5.6以后），MySQL会自动返回记录插入
5. `TIMESTAMP`是UTC时间戳，与时区无关
6. `DATETIME`的存储格式是一个`YYYYMMDD HH:MM:SS`的整数，与时区无关
7. 除非有特殊需求，建议使用`TIMESTAMP`，它比`DATETIME`更节约空间

**字符串**

> CHAR：定长字符串
>
> VARCHAR：变长字符串
>
> TINYBLOB：不超过255个字符的二进制字符串
>
> TINYTEXT：短文本字符串
>
> BLOB：二进制形式的长文本数据
>
> TEXT：长文本数据
>
> MEDIUMBLOB：二进制形式的中等长度文本数据
>
> MEDIUMTEXT：中等长度文本数据
>
> LONGBLOB：二进制形式的极大文本数据
>
> LONGTEXT：极大文本数据

**优化建议**

1. 字符串的长度相差较大用`VARCHAR`，字符串短，且所有值都接近一个长度用`CHAR`
2. `CHAR`和`VARCHAR`适用于包括人名、邮政编码、电话号码和不超过255个字符长度的任意字母数字组合，那些要用来计算的数字不要用`VARCHAR`类型保存，会影响到计算的准确性和完整性
3. `BINARY`和`VARBINARY`存储的是二进制字符串，与字符集无关
4. `BLOB`系列存储二进制字符串，与字符集无关，`TEXT`系列存储非二进制字符串，与字符集相关。一般情况下，可以认为`BLOB`是一个更大的`VARBINARY`，`TEXT`是一个更大的`VARCHAR`
5. `BLOB`和`TEXT`都不能有默认值

**INT显示宽度**

```mysql
CREATE TABLE `user`(
	`id` TINYINT(2) UNSIGNED
);
```

这里的长度并非是`TINYINT`类型存储的最大长度，而是显示的最大长度，这里表示user表的id字段的类型是`TINYINT`，可以存储的最大数值是`255`，在存储数据时，如果存入值小于等于`255`，如`200`，虽然超过2位但是没有超出`TINYINT`类型长度，所以可以正常保存。

```mysql
`id` TINYINT(2) UNSIGNED ZEROFILL
```

这里的`TINYINT(2)`的作用是，位数不满足时，前面用0填充