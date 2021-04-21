## Plan9

Go 语言使用 plan9 汇编

### 获取go的汇编方式

- `go tool compile -N -l -S xx.go`

- ```shell
  go tool compile -N -l xx.go  # 编译程序
  go tool objdump xx.o	# 使用 objdump 反汇编出代码
  go tool objdump -s {symbol} xx.0 # 可以输出指定的symbol
  ```

- `go build -gcflags -S xx.go`

### 常量

常量总是 64 位无符号整数

### 符号

go 预定义了一些伪寄存器，这些伪寄存器适用所有的架构

- FP：Frame pointer（栈帧指针）：arguments and locals
- PC：Program counter（程序计数器）：jumps and branches
- SB：Static base pointer（静态指针）：global symbols
- SP：Stack pointer（栈顶指针）：top of stack

#### SB

> 用户定义的符号都会按照偏移量写在 FP 和 SB中
>
> SB 可以被看做是起始内存，foo(SB) foo 是一个内存地址中的别名。经常当做全局函数和变量。
>
> foo<>(SB) 使这个foo仅在当前的 source file 可见
>
> foo+4(SB) 指从foo这个地址偏移4个字节

#### FP

> FP 是一个虚拟的栈帧指针，用来引用函数参数
>
> 0(FP) 是函数的第一个参数，8(FP) 是函数的第二个参数( 64 bit 机器)
>
> 如果这样调用 0(FP) 是会报错，它们需要一个具体的名字来使用 first_arg+0(FP) 表示第一个函数的地址。
>
> FP 总是一个伪地址即使是在一个具有 FP 的架构上。

#### SP

> SP 一个虚拟的栈顶指针，指向一个局部栈帧和参数。因为一直指向栈顶所以在使用时应该是，x-8(SP),y-4(SP)
>
> 在一个具有 SP 的架构上，SP 的名字前缀将伪寄存器与实际寄存器分开
>
> - x-8(SP) 表示一个伪寄存器
> - -8(SP) 实际寄存器
>
> 一般 SP 和 PC 都是一个物理寄存器的别名。在 go 汇编中区别对待了它们。go中使用需要一个 symbol，first_arg+0(FP)，而为了访问实际寄存器上的值，需要使用 R 。如 R13，R15

### 指令

#### TEXT 

text 指令的最后一条指令必须是 jump，ret 是一个伪指令。

如果不添加，linker 会自动添加一个 jump-to-itself 的指令

```
TEXT runtime·profileloop(SB),NOSPLIT,$8
	MOVQ	$runtime·profileloop1(SB), CX
	MOVQ	CX, 0(SP)
	CALL	runtime·externalthreadhandler(SB)
	RET
```

栈帧大小即使参数大小，通过 - 分隔。如：$24-8 表示函数有 24 byte 的栈帧和调用参数具有 8 byte，都存在于调用者的栈帧中。

如果没有 NOSPLIT 在 text 中，则参数大小必须指定

符号名字使用 *·* 分隔组件和名字。如 `$runtime·profileloop1(SB)` 表示这个函数会调用来自runtime包的一个变量名为 profileloop 的变量

#### DATA

全局变量被 DATA 和 GLOBL 指令定义

每个 DATA 指令都会初始化一块对应的内存，没有明确初始化的内存会被清零

```
DATA	symbol+offset(SB)/width, value
symbol 在 SB 的 offset 上，value 的大小是 width

DATA divtab<>+0x3c(SB)/4, $0x81828384
```

#### GLOBL

```
GLOBL divtab<>(SB), RODATA, $64
divtab<>，4 byte值在一个只读的 64 byte 表上
GLOBL runtime·tlsoffset(SB), NOPTR, $4
```

