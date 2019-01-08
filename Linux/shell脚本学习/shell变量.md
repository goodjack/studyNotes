### 普通变量

- 变量赋值方式：`str=value` ，其实是 `set str=value` ，省略了 set 关键字（注意等号左右没有空格）
- 变量引用方式：`$str` 或 `{str}`
- 释放变量：`unset str`
- 查看所有的变量：不接受任何参数的 set 或者 declare 命令，输出结果中包含了普通变量和环境变量
- 定义只读变量：`readonly str` ，这时将无法修改变量值也无法 unset 变量，只有重新登录 shell 才能继续使用只读变量
- 临时将普通变量升级为环境变量：`expport str` 或者赋值时 `export str="value"` ，此变量可以在当前 shell 和子 shell 中使用，当退出脚本或者重新登录 shell 都会取消 export 效果。

### 位置变量和特殊变量

- `$?` ：上一条代码执行的回传指令，0 表示标准输出。
- `$$` ：当前的 shell 的 PID。除了执行 bash 和 shell 时，`$$` 不会继承父 shell 的值，其他类型的子 shell 都继承。
- `$BASHPID` ：当前 shell 的 PID，与 `$$` 不同，每个 shell 的 `$BASHPID` 是独立的，而 `$$` 会继承父 shell 的值。
-  `$!` ：最近一次执行的后台进程 PID
- `$#` ：统计参数的个数
- `$@` ：所有单个参数
- `$*` ：所有参数的整体
- `$0` ：脚本名
- `$1.....$n` ：参数位置



shift N ：指定参数轮替，每执行一次就踢掉 N 个参数

### 变量赋值

| 变量赋值                   | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| ${parameter:-word}         | 如果 parameter 为空或未定义，则变量为 Word，否则变量为parameter的值 |
| ${parameter-word}          | 和 ${parameter:-word}几乎等价，除了parameter设置了但为空时，变量的结果将是null，而非Word |
| ${parameter:+word}         | 如果parameter为空或未定义，变量为空或未定义，否则为 word     |
| ${parameter:=word}         | 如果parameter为空或未定义，则变量为 word，否则为 parameter   |
| ${parameter:offset}        | 取子串，从offset处的后一个字符开始取到最后一个字符           |
| ${parameter:offset:length} | 取子串，从offset处的后一个字符开始，取lenth长的子串          |

