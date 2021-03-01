

### PProf

采样方式：

- runtime/pprof：采集程序指定区块的数据进行分析
- net/http/pprof：基于HTTP Server，且可以采集运行时的数据进行分析
- go test：基于测试用例，指定所需标识进行采集

可查看信息：

- allocs：查看过去所有内存分配样本
- block：查看导致阻塞同步的堆栈跟踪

```go
// 阻塞采集需要设置
runtime.SetBlockProfileRate(1)
```

- cmdline：当前程序命令行完整调用路径
- goroutine：查看当前所有运行的 goroutine 堆栈跟踪
- heap：查看活动对象的内存分配情况
- mutex：查看导致互斥锁的竞争持有者的堆栈跟踪

```go
// 对于互斥锁的采集需要设置
runtime.SetMutexProfileFraction(1)
```

- profile：默认进行30s的CPU profiling
- threadcreate：查看创建新 OS 线程的堆栈跟踪

下载下来的文件可以使用`go tool pprof {file}`

参数含义：

- flat：函数自身运行耗时
- flat%：函数自身占CPU运行总耗时的比例
- sum%：函数自身累积使用占CPU运行总耗时比例
- cum：函数自身及其调用函数的运行总耗时
- cum%：函数自身及其调用函数占CPU运行总耗时的比例
- Name：函数名

> 在pprof终端内，执行list命令时，从远程拉取的profile，本机和原本运行主机一般时不同，需要 go tool pprof 指定 source_path 参数



### Trace

主要查看goroutine的创建、阻塞、解除阻塞，syscall的进入、退出、阻止，GC事件、调整Heap的大小，以及Processor启动、停止等。

可视界面列表：

- view trace：查看跟踪
- goroutine analysis：goroutine 分析
- network blocking profile：网络阻塞概况
- synchronization blocking profile：同步阻塞概况
- syscall blocking profile：系统调用阻塞概况
- scheduler latency profile：调度延迟概况
- user defined tasks：用户自定义任务
- user defined regions：用户自定义区域
- minimum mutator utilization：最低 mutator 利用率



### GODEBUG 查看调度跟踪

查看调度器或垃圾回收等详细信息

`GODEBUG=schedtrace=1000 go run main.go` 输出调度器的摘要信息

- gomaxprocs：当前的CPU核心数，受GOMAXPROCS控制
- idleprocs：空闲的处理器数量
- threads：OS线程数量
- spinningthreads：自旋状态的OS线程数量
- idlethreads：空闲的线程数量
- runqueue：全局队列的goroutine数量
- runqsize：运行队列中的G数量
- gfreecnt：可用的G，状态为Gdead
- schedtick：P的调度次数
- syscalltick：系统调用次数
- preemptoff：如果不等于空字符串，则保持curg在这个M上运行

增加scheddetail参数，可以提供处理器P，线程M和goroutine G 的细节

```go
GODEBUG=schedtrace=1000,scheddetail=1 go run main.go
```

摘要信息中，G的status状态列表：

| 状态             | 值   | 含义                                                         |
| ---------------- | ---- | ------------------------------------------------------------ |
| Gidle            | 0    | 刚刚被分配，还没有进行初始化                                 |
| Grunnable        | 1    | 已经在运行队列中，还没有执行用户代码                         |
| Grunning         | 2    | 不在运行队列中，已经可以执行用户代码，此时已经分配了M和P     |
| Gsyscall         | 3    | 正在执行系统调用，此时分配了M                                |
| Gwaiting         | 4    | 在运行时被阻止，没有执行用户代码，也不再运行队列中，此时正在某处阻塞等待中 |
| Gmoribund_unused | 5    | 尚未使用，但是在gdb中进行了硬编码                            |
| Gdead            | 6    | 尚未使用，这个状态可能时刚退出或是刚被初始化，此时它没有执行用户代码，有可能是没有分配堆栈 |
| Genqueue_unused  | 7    | 尚未使用                                                     |
| Gcopystack       | 8    | 正在复制堆栈，并没有执行用户代码，也不在运行队列中           |

p的状态

| 状态     | 值   | 含义                                                       |
| -------- | ---- | ---------------------------------------------------------- |
| Pidle    | 0    | 刚刚被分配，还没有进行初始化                               |
| Prunning | 1    | 当M与P绑定调用 acquirep 时，P的状态会变为Prunning          |
| Psyscall | 2    | 正在执行系统调用                                           |
| Pgcstop  | 3    | 暂停运行，此时系统正在进行GC，直至GC结束后才会进入下一阶段 |
| Pdead    | 4    | 废弃，不再使用                                             |



### gops 进程诊断工具