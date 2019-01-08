## read 命令

用于从标准输入中读取输入单行，并将读取的单行根据 IFS 变量分裂成多个字段，并将分割后的字段分别赋值给指定的变量列表 var_name 。

如果指定的变量名少于字段数量，则多出的字段数量也同样分配给最后一个 var_name。如果指定的变量名多于字段数量，则多出的变量赋值为空。如果没有指定任何 var_name，则分割后的所有字段都存储在特定变量 REPLY 中。参见 [read](http://www.cnblogs.com/f-ck-need-u/p/7402149.html)

## date 命令

用于获取和设置操作系统的时间，`hwclock` 是获取硬件时间，参见 [date,sleep](http://www.cnblogs.com/f-ck-need-u/p/7427262.html)

### sleep 和 usleep 命令

sleep 默认单位秒

### tr 命令

将从标准输入读取的数据进行结果集映射、字符压缩和字符删除。会将读取的标准输入进行排序然后按照某种方式换行，再根据命令做相应处理，详细参见 [tr命令用法和特性全解](http://www.cnblogs.com/f-ck-need-u/p/7521506.html)

### cut 命令

将行按指定的分隔符分割成多列，弱点在于不好处理多个分割符重复的情况，因此结合 tr 的压缩功能。参见 [cut](http://www.cnblogs.com/f-ck-need-u/p/7521357.html.html)

### sort 命令

读取每一行输入，并按照指定的分隔符将每一行划分成多个字段，这些字段就是 sort 排序的对象，同时 sort 可以指定按照何种排序规则进行排序，默认按照当前字符集排序规则，详细参见 [sort](http://www.cnblogs.com/f-ck-need-u/p/7442886.html)

### uniq 命令

去重，不相邻的行不算重复值，参见 [uniq](http://www.cnblogs.com/f-ck-need-u/p/7454597.html)

### seq 命令

用于输出数字序列。支持正数序列、负数序列、小数序列，参见 [seq](http://www.cnblogs.com/f-ck-need-u/p/7454621.html)

### functions 文件

`/etc/rc.d/init.d/functions` 几乎被 `/etc/rc.d/init.d` 下的所有 `Sysv` 服务启动脚本加载，详细参见 [functions](http://www.cnblogs.com/f-ck-need-u/p/7518142.html)

### find 命令

详细参考 [find](http://www.cnblogs.com/f-ck-need-u/p/6995529.html)

### xargs 命令

xargs 处理管道传输过来的 stdin，然后将处理后的参数传递到正确的位置上。

