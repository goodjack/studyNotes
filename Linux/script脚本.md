# 脚本编写

注意事项：

- 直接命令执行：shell.sh文件必须要具备可读与可执行（rx）权限

  - 绝对路径：使用/home/dmtsai/shell.sh
  - 相对路径：假设工作目录在/home/dmtsai/，则用./shell.sh
  - 变量“PATH”功能：通过bash shell.sh 或 sh shell.sh 来执行

  sh shell.sh ，/bin/sh是/bin/bash（连接文件）

- 第一行`#!/bin/bash`声明这个script使用的shell名称

- 程序内容的说明

  - 第二行用来说明script的内容和功能、版本信息、作者和联系方式、建立日期

- 主要环境变量的声明

  - 设置PATH和LANG`PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin		export PATH`

- 主要程序部分

- 告知执行结果

tips：脚本内使用read命令时，输入错误时无法删除的解决方法，在read命令前加上`stty erase ^H`

- source执行脚本：在父进程中执行，可以输出脚本内变量，sh或bash不行
- test等于`[]`，使用`[]`需要注意左右两端有空格，变量需要用`""`包起来
- shell script默认参数：
  - **$#：**代表后接的参数个数
  - **$@：**代表`$1,$2,$3,$4`，每个变量时独立的
  - **$*：**代表`$1c$2c$3c$4`,c为分隔符，默认空格
- shift：从左往右移除参数，默认移除一个，可以指定移除数量，shift n
- shell内也可以写function，function也有内置变量`$0,$1`等

### 循环

#### 不定循环（while do done，until do done）

```
while [ condition ]	#条件
do	#开始循环
	内容
done	#结束循环
```

```
until [ condition ]	#条件
do	#开始循环
	内容
done	#结束循环
```

