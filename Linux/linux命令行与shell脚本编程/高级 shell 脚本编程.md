# 函数

函数局部变量：`local` 关键字定义函数的局部变量

#### 创建库

使用函数库 source 命令，source 命令有个快捷别名，称作点操作符`.`

```bash
# myfuncs include add reduce
#想在这个脚本使用 myfuncs 库文件，这里假设脚本文件和库文件在同一路径
. ./myfuncs
```



**创建菜单 select 命令**

```bash
select var in list 
do
	commands
done

# 例
PS3="Enter option:" #修改环境变量 PS3 展示一个自定义的提示符
select opt in "Display disk space" "Display logged on users" "Display memory usage" "Exit program"
do
	case "$opt" in
	"Exit program")
	.
	.
	.
done

#opt 的变量值是整个文本字符串不是数字

```



## sed 和 gawk 编辑器

### sed

sed 被称作流编辑器

sed 命令格式：`sed options script file`

**替换标记**

`s/pattern/replacement/flags`

flags 有四个选项：

- 数字，表明新文本将替换第几处模式匹配的地方
- g，表明新文本将会替换所有匹配的文本
- p，表明原先行的内容要打印出来
- w file，将替换的结果写到文件中

>  替换字符，有时会遇到一些特殊的字符。例如，使用文件名使用正斜线 / 
>
> `sed 's/\/bin\/bash\/bin\/csh/' /etc/passwd` 这种不利于观看
>
> sed 允许选择其它字符作为替换命令中的字符串分隔符
>
> `sed 's!/bin/bash!/bin/csh!' /etc/passwd`

**使用地址**

sed 编辑器中使用的命令会作用于文本数据的所有行，如果想将命令只作用于特定行，则须使用行寻址。

sed 支持两种行寻址：

- 以数字形式表示行区间：`sed '2s/dog/cat/' file` ，只替换了第二行的文本
- 用文本模式过滤出行：`sed '/user/s/bash/zsh' /etc/passwd` ，先匹配中 user 然后再替换指定的文本

**删除行**

- 指定行号：`sed '3d' file`
- 指定行区间：`sed '2,$d' file` $ 是一个特殊的文件结尾字符
- 模式匹配：`sed '/number 1/d' file`，匹配到 number 再删除一行

**插入和追加文本**

- 插入：`echo "Test line 2" | sed 'i\Test Line 1'`，这个会把 Test line 1 插入到数据流文本前
- 追加：`echo "Test Line 2" | sed 'a\Test Line 1'`
- 插入和追加都可以使用行地址模式 `number i|a`

**修改行**

`sed '3c' file` ，也支持使用匹配模式

**字符转换**

`sed 'y/123/789/' file`，这会逐字符的替换每个字符，inchars 和 outchars 长度必须一致

**打印行号** 

`sed '=' file`

**列出行**

`echo 'This line contains	tabs' | sed -n 'l'`

单行 ， n 命令会告诉 sed 编辑器移动数据流到下一文本行。

多行 ，N 命令会将下一文本行添加到模式空间中已有的文本后。作用是将数据流中的两个文本行合并到同一个模式空间中，文本行任然用换行符分隔，但 sed 编辑器会将两行文本当成一行处理。

多行删除，D 命令它只会删除模式空间中的第一行，会删除到换行符（含换行符）为止的所有字符。

#### sed 保持空间

**模式空间：sed 编辑器的工作空间，是一块活跃的缓冲区**

**保持空间：在处理模式空间中的某些行时，可以用保持空间来临时保存一些行**

**保持空间的命令**

| 命令 | 描述                         |
| ---- | ---------------------------- |
| h    | 将模式空间复制到保持空间     |
| H    | 将模式空间附加到保持空间     |
| g    | 将保持空间复制到模式空间     |
| G    | 将保持空间附加到模式空间     |
| x    | 交换模式空间和保持空间的内容 |

#### 分支

分支命令格式：`[address]b label`

**如果没有定义 label 标签，跳转命令会跳转到脚本尾部。指定了标签则可以跳过地址匹配出的命令**

```bash
cat data2.txt
This is the header 1 line.
This is the first data line.
This is the second data line.
This is the last line.

sed '{2,3b ; s/This is/Is this/;s/line./test?/}' data2.txt
# 此处没有定义 label 标签会跳转到脚本尾部，相当于不会对 2 3 行执行脚本

sed '{/first/b jump1;s/This is the/No jump on/; :jump1;s/This is the/Jump here on/}' data2.txt
# 当一行内匹配到 first 字符，就跳转到 jump1，这样就会跳过 jump1 前面的脚本命令
```

#### 测试

测试命令会根据替换命令的结果跳转到某个标签，如果替换命令成功匹配并替换了一个模式，测试命令就会跳转到指定的标签。如果未指定标签，则跳转到脚本结尾。

```bash
echo "This, is, a, test, to, remove, commas." | sed -n '{:start;s/,//1p; t start}'
# t 命令相当于 if 判断，如果 t 成功则跳转到指定标签，否则不跳转
```

#### 模式替代

& 符号可以用来代表替换命令中的匹配模式

```bash
echo "The cat sleeps in his hat." | sed 's/.at/"&"/g'
# 因为点符号代表的是未知字符，无法使用 .at 这样的字符替换，sed 提供了 & 符号来代表匹配到的整个字符
# 也可以使用正则中的组匹配模式 (.at)，需要注意 sed 中使用组匹配模式需要给括号加转义符 \(.at\)
```



### gawk

gawk 数据字段变量：`$0` 代表整行，`$1 -$n` 代表文本行中的 n 个数据字段。每个数据字段都是通过**字段分隔符划分** 。gawk 默认字段分隔符是任意的空白字符

在处理数据前运行脚本 `BEGIN`关键字，`gawk 'BEGIN {print "Hello World!"}'` 

在处理数据后运行脚本 `END` 关键字

**使用花括号表示间隔，必须为 gawk 指定 --re-interval 选项**

`echo "bt" | gawk --re-interval '/be{1}t/{print $0}'` 

#### 变量

gawk 使用两种变量：内建变量和自定义变量

**内建变量**

| 变量        | 描述                                             |
| ----------- | ------------------------------------------------ |
| FILEDWIDTHS | 由空格分隔的一列数字，定义了每个数据字段确切宽度 |
| FS          | 输入字段分隔符                                   |
| RS          | 输入记录分隔符                                   |
| OFS         | 输出字段分隔符                                   |
| ORS         | 输出记录分隔符                                   |

**数据变量**

| 变量       | 描述                                                   |
| ---------- | ------------------------------------------------------ |
| ARGC       | 当前命令行参数个数                                     |
| ARGIND     | 当前文件再 ARGV 中的位置                               |
| ARGV       | 包含命令行参数的数组                                   |
| CONVFMT    | 数字转换格式，默认 %.6 g                               |
| ENVIRON    | 当前 shell 环境变量及其值组成的关联数组                |
| ERRNO      | 当读取或关闭输入文件发生错误时的系统错误号             |
| FILENAME   | 用作 gawk 输入数据的数据文件的文件名                   |
| FNR        | 当前数据文件的数据行数                                 |
| IGNORECASE | 设成非零值时，忽略 gawk 命令中出现的字符串的字符大小写 |
| NF         | 数据文件的字段总数                                     |
| NR         | 已处理的输入记录数                                     |
| OFMT       | 数字的输出格式，默认值 %.6 g                           |
| RLENGTH    | 由 match 函数所匹配的子字符串的长度                    |
| RSTART     | 由 match 函数所匹配的子字符串的起始位置                |

**匹配操作：~**

```bash
$1 ~ /^data/  # 表示了匹配第一个数据字段开头包含 data 的数据
```

**数学表达式：**

```bash
gawk -F : '$4 == 0{print $1}' /etc/passwd 
# 表示匹配第4个数据字段值为 0 的数据行
```

**结构化命令：**正常的格式化语句

**格式化打印：gawk 使用 printf 打印，与 C 语言一致**

| 控制字母 | 描述                        |
| -------- | --------------------------- |
| c        | 将一个数作为 ASCII 字符显示 |
| d / i    | 显示一个整数值              |
| e / g    | 科学计数法显示一个数        |
| f        | 显示一个浮点值              |
| o        | 显示八进制                  |
| s        | 显示一个文本字符串          |
| x        | 十六进制                    |
| X        | 十六进制，使用大写的 A-F    |

#### 内建函数：

数学函数：

| 函数       | 描述                              |
| ---------- | --------------------------------- |
| atan2(x,y) | x/y 的反正切，x 和 y 以弧度为单位 |
| cos(x)     | x 的余弦，x 以弧度为单位          |
| exp(x)     | x 的指数函数                      |
| int(x)     | x 的整数部分，取靠近零一侧的值    |
| log(x)     | x 的自然对数                      |
| rand()     | 比 0 大比 1 小的随机浮点值        |
| sin(x)     | x 的正弦，x 以弧度为单位          |
| sqrt(x)    | x 的平方根                        |
| srand(x)   | 为计算随机数指定一个种子值        |

字符串函数：

| 函数                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| asort(s,[,d])       | 将数组 s 数据按元素值排序。如果指定了 d ，排序后的数组会储存再数组 d 中 |
| asorti(s,[,d])      | 将数组 s 按索引值排序。生成的数组会将索引值作为数据元素值。  |
| gensub(r,s,h,[,t])  | 查找变量 $0 或目标字符串 t 来匹配正则表达式 r。如果 h 是一个以 g 或 G 开头的字符串，就用 s 替换掉匹配文本。如果 h 是一个数字，它表示要替换掉第 h 处 r 匹配的地方 |
| gsub(r,s,[,t])      | 查找变量 $0 或目标字符串 t 来匹配正则表达式，如果找到就全部替换成字符串 s |
| index(s,t)          | 返回字符串 t 在字符串 s 中的索引值，如果没找到返回 0         |
| length([s])         | 返回字符串 s 的长度，如果没有指定 s ，则返回 $0 的长度       |
| match(s,r,[,a])     | 返回字符串 s 中正则表达式 r 出现位置的索引。如果指定了数组 a，它会储存 s 中匹配正则表达式的那部分 |
| split(s,a,[,r])     | 将 s 用 FS 字符或正则表达式 r 分开放到数组 a 中，返回字段的总数 |
| sprintf(format,var) | 用提供的 format 和 var 返回一个类似 printf 输出的字符串      |
| sub(r,s,[,t])       | 在变量 $0 或目标字符串 t 中查找正则表达式 r 的匹配。如果找到就用 s 替换第一处匹配 |
| substr(s,i,[,n])    | 返回 s 中从索引值 i 开始的 n 个字符组成的子字符串。如果 n 未提供，则返回 s 剩下的部分。 |
| tolower(s)          | 转为小写                                                     |
| toupper(s)          | 转为大写                                                     |

时间函数：

| 函数                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| mktime(datespec)              | 将一个按 YYYY MM DD HH MM SS [DST] 格式指定的日期转成时间戳  |
| strftime(format,[,timestamp]) | 将当前时间的时间戳或 timestamp 转化格式化日期（采用 shell 函数的 date() 格式） |
| systime()                     | 返回当前时间的时间戳                                         |

