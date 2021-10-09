### 显示消息

echo 命令



### 管道

管道符号 `|`

**由管道串起的命令不会依次执行，Linux 会同时运行这些命令，在系统内部将它们连接起来。第一个命令产生输出同时，输出会立即传给第二个命令。数据传输不会用到任何中间文件或缓冲区。**



### 数学表达式

expr 命令 这个命令处理数学表达式比较复杂，不推荐

`$[ operation ]` 使用此种方法也可以执行数学表达式

Tips: 

**bash 数学运算符只支持整数运算，zsh 提供了完整的浮点数运算**

使用 bc 命令可以进行浮点数运算



### 常用 Linux 退出状态码

| 状态码 | 描述                       |
| ------ | -------------------------- |
| 0      | 命令成功结束               |
| 1      | 一般性未知错误             |
| 2      | 不适合的 shell 命令        |
| 126    | 命令不可执行               |
| 127    | 没找到命令                 |
| 128    | 无效的退出参数             |
| 128+x  | 与 Linux 信号 x 相关的错误 |
| 130    | 通过 ctrl + c 终止命令     |
| 255    | 正常范围之外的退出状态码   |

退出状态码最大值只能是 **255**



## 结构化命令

####  if - then

```bash
if command
then
	command
fi

# 另一种形式
if command;then
	commands
fi
```



bash 的 if 语句会运行 if 后面的那个命令。如果该命令的退出状态码是 0 位于 then 部分的命令就会被执行



test 命令提供了在 if-then 中测试不同条件的途径。如果 test 命令中列出的条件成立，test 命令就会退出并返回退出状态码 0。

另一种测试方法 

```bash
if [ condition ];then
	commands
fi

# elif 
if [condition]; then
	commands
elif [condition];then
	commands
else
	commands
fi
```

#### 数值比较

| 比较      | 描述                      |
| --------- | ------------------------- |
| n1 -eq n2 | 检查 n1 是否与 n2 相等    |
| n1 -ge n2 | 检查 n1 是否大于或等于 n2 |
| n1 -gt n2 | 检查 n1 是否大于 n2       |
| n1 -le n2 | 检查 n1 是否小于或等于 n2 |
| n1 -lt n2 | 检查 n1 是否小于 n2       |
| n1 -ne n2 | 检查 n1 是否不等于 n2     |

#### 字符串比较

| 比较    | 描述                     |
| ------- | ------------------------ |
| -n str1 | 检查 str1 的长度是否非 0 |
| -z str1 | 检查 str1 的长度是否为 0 |

字符串比较的陷阱：

- 大于号和小于号必须转义，否则 shell 会把它们当作重定向符号
- 大于和小于顺序和 sort 命令所采用的排序不同，造成这个的原因是：比较测试中使用的是标准的 ASCII 顺序，根据每个字符的 ASCII 数值来决定排序结果。sort 命令使用的是系统的本地化语言设置中定义的排序顺序

### 文件比较

| 比较            | 描述                                       |
| --------------- | ------------------------------------------ |
| -d file         | 检查 file 是否存在并是一个目录             |
| -e file         | 检查 file 是否存在                         |
| -f file         | 检查 file 是否存在并是一个文件             |
| -h file         | 检查 file 是一个文件并且是一个软链接       |
| -L file         | 检查 file 是一个文件并且是一个符号链接     |
| -r file         | 检查 file 是否存在并可读                   |
| -s file         | 检查 file 是否存在并非空                   |
| -w file         | 检查 file 是否存在并可写                   |
| -x file         | 检查 file 是否存在并可执行                 |
| -O file         | 检查 file 是否存在并属当前用户所有         |
| -G file         | 检查 file 是否存在并且默认组与当前用户相同 |
| file1 -nt file2 | 检查 file1 是否比 file2 新                 |
| file1 -ot file2 | 检查 file1 是否比 file2 旧                 |

**双括号允许在比较过程中使用高级数学表达式**

`(( expression ))`

**双括号命令符号**

![](assets/双括号命令符号.png)

**双方括号提供了针对字符串比较的高级特性,使用了 test 的标准字符串比较并且提供了模式匹配的特性**

`[[ expression ]]`



#### case 命令

```bash
case var in
pattern1)
	commands;
pattern2 | pattern3)
	commands;
*) 
	default commands;
esac
```



#### for 命令

```bash
for var in list
do
	commands;
done

# for 循环
for((a=1;a<10;a++));do
	echo "$a"
done
```

tips:

for 命令默认使用空格划分值

**读取列表中的复杂值，如果有单引号或者空格需要转义或者使用双引号包裹**



### while 命令

```bash
while test command
do
	commands
done
```

while 命令的关键在于所指定的 test command 的退出状态码必须随着循环中运行的命令而改变，如果状态码不改变，循环将不会停止。



### until 命令

until 命令要求指定一个返回非零退出状态码的测试命令，只有退出状态码不为 0，循环才会终止。

```bash
until test commands
do
	commands
done
```

**tips：while 和 until 命令只有在最后一个命令成立时才会停止**



break 命令接受单个命令行参数值：`break n` n 指定了要跳出的循环层级。continue 同。



### 处理循环输出

```bash
for var in $HOME;do
	commands
done > output.txt # 将循环输出重定向到了 output 文件

for var in $HOEM;do
	commands
done | sort # 将循环的结果通过管道传输给 sort
```



## Tips：IFS 环境变量可以修改默认文件分隔符，注意保存原始 IFS 值





# 处理用户输入

bash 脚本读取命令行参数会将位置参数分配给特殊变量，位置参数变量是标准的数字：`$0 是程序名，$1 是第一个参数，以此类推，直到第九个参数`



### 特殊参数变量

`$#` 携带命令行参数的个数

`${!#}` 可以取出最后一个命令行参数

获取所有参数：

- `$*` 变量会将所有参数当成单个参数
- `$@` 变量会单独处理每个参数

移动变量：shift，默认情况下会将每个参数向左移动一个位置（`$0` 值不会改变，从 `$1` 开始移动）

#### getopts 命令

格式：`getopts optstring variable`

有效的选项字母都会列在 optstring 中，如果选项字母要求有个参数值，就加一个冒号。要去掉错误消息，在 optstring 之前加一个冒号。getopts 将当前参数保存在命令行定义的 variable 中。

getopts 使用两个环境变量。如果一个选项需要参数值，OPTARG 环境变量就会保存值。OPTIND 环境变量保存了参数列表中 getopts 正在处理的参数位置。

```bash
while getopts :ab:cd opt;do
	case "$opt" in
		a) echo "xxxx"
		b) echo "xxxx $OPTARG"
		*) echo "xxxx"
	esac
done
shift $[ $OPTIND - 1 ] # 移除前面的参数
```

##### 常用的标准化 Linux 选项

| 选项    | 描述                       |
| ------- | -------------------------- |
| -a      | 显示所有对象               |
| -c      | 生成一个计数               |
| -d      | 指定一个目录               |
| -e      | 扩展一个对象               |
| -f      | 指定读入数据的文件         |
| -h      | 显示帮助信息               |
| -i      | 忽略文本大小写             |
| -l      | 产生输出的长格式版本       |
| -n      | 使用非交互模式             |
| -o      | 将所有输出重定向到指定文件 |
| -q / -s | 以安静模式运行             |
| -r      | 递归处理目录和文件         |
| -v      | 生成详细输出               |
| -x      | 排除某个对象               |
| -y      | 对所有问题 yes             |

### 获得用户输入

 read 命令格式：`read variable` ，也可以不指定变量，这样收到的任何数据都会存入 REPLY 环境变量中





### 标准文件描述符

lsof 命令列出打开的文件描述符

#### 临时重定向

```bash
echo "This is error" >&2 # 这一行是将输出临时重定向到 STDERR
echo "This is normal output"
```

#### 永久重定向

exec 命令告诉 shell 脚本执行期间重定向某个特定文件描述符

在脚本中重定向输入：

```bash
exec 0< testfile
while read line # read 读取了 testfile 里的内容
do
	echo "$line"
done
```

#### 重定向文件描述符

```bash
exec 3>&1 # 将 3 文件描述符重定向到了 STDOUT
exec 1>testout # 将 STDOUT 重定向到了 testout
echo "xxxxx"
echo "xxxxxxxxx"
# 此时的 STDOUT 被重定向到了 testout 文件中
exec 1>&3 # 此时 3 是 STDOUT ，这步操作时将 1 重新变成 STDOUT 

# 重定向输入文件描述符
exec 6<&0 # 将 STDIN 重定向到 6 文件描述符
exec 0< testfile # 此时 STDIN 被重定向到了 testfile
commands
exec 0<&6 # 将 6(STDIN) 重新赋给 0
```

**关闭文件描述符**：`exec 3>&-`

### 创建临时文件

mktemp 命令会在 /tmp 目录中创建一个唯一的临时文件

```bash
# 文件模板，模板可以包含任意文件名，在文件名末尾加上 6 个 X ，mktemp 会用6个字符码替换6个x，保证每个文件唯一
mktemp name.xxxxxx
```

### 记录消息

tee 命令相当于管道的一个 T 型接头，它将从 STDIN 过来的数据同时发往两处。一处是 STDOUT，另一处是 tee 命令所指定的文件



## 控制脚本

##### 捕获信号

trap 命令格式：`trap commands signals `

```bash
# 会捕获到 SIGINT 信号，然后输出一行文本
trap "echo ' Sorry，trapped CTRL + C'" SIGINT
# 捕获脚本退出
trap "echo Goodbye..." EXIT
# 修改或移除捕获
trap -- SIGINT
```

**调整谦让度，nice 命令，-20（最高优先级）到19（最低优先级）**

renice 修改优先级



#### 定时运行作业

at 命令和 cron 命令

cron 格式：`min hour dayofmonth month dayofweek command`

如果创建的脚本对精确的执行时间要求不高，可以用预配置的 cron 脚本目录。有 4 个基本目录：hourly、daily、monthly、weekly

**Tips：cron 程序会用提交作业的用户账号运行脚本，因此必须有访问该命令和命令中指定的输出文件的权限**

#### anacron 程序

anacron 如果知道某个作业错过执行时间，它会尽快运行该作业

格式：`period delay identifier command`

period 定义作业多久运行一次，以天为单位

delay 指定系统启动后 anacron 需要等待多少分钟再开始运行错过的脚本

identifier 非空字符串，用于唯一标识日志消息和错误邮件中的作业

command 包含了 run-parts 程序和一个 cron 脚本目录名，run-parts 负责运行目录中传给它的任何脚本

```bash
#period in days     delay in minutes    jobs-identifier		command
		1					5			 cron.daily		nice run-parts /etc/cron.daily
```

