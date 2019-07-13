### 查看网络端口和状态 netstat

### 查看网络配置 ipconfig

### 查看路由信息 route print

### 查看环境变量 `$env:path`

> 默认键入一个字符串，powershell 会原样输出，如果字符串是一个命令或者启动程序，在字符串前加 `&` 可以执行命令，或者启动程序

### 启动 CMD 控制台 cmd 退出 exit

### 查找可用的 cmd 控制台命令 `cmd /c help`



# 变量

查看正在使用的变量：`ls variable:`

查找变量和验证变量是否存在：

- `Test-Path variable:value*`  查找变量支持通配符 

- `Test-Path variable:value1`  验证变量是否存在
- `del variable:value` 删除变量
- 使用专用的变量命令：`Clear-Variable,Get-Variable,New-Variable,Remove-Variable,Set-Variable` ，clear，remove，set 的命令可以被代替，`New-Variable` 可以在定义变量时，指定变量的一些其它属性，比如访问权限

### 自动化变量

### 环境变量

查看环境变量：`ls env:`

获取指定环境变量：`$env:name`

变量作用域：

- `$global` 全局变量，在所有作用域中有效，如果在脚本或函数中设置全局变量，即使脚本和函数都运行结束，这个变量也任然有效
- `$script` 脚本变量，只在脚本内部有效
- `$private` 私有变量，只在当前作用域有效
- `$local` 默认变量，可以省略，在当前作用域有效，其它作用域只对它有只读权限



