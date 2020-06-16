### defer

Go 语言的 `defer` 会在当前函数或者方法返回之前执行传入的函数。

**defer 预计算参数**

```go
startedAt := time.Now()
defer fmt.Println(time.Since(StartedAt))	// 输出 0s 不符合预期，这是因为 defer 对于参数的计算是在 defer 关键字调用时计算，而不是在 main 函数退出之前计算

// 正确的写法
defer func() {
  fmt.Println(time.Since(startedAt))
}

time.Sleep(time.Second)
```



### panic

**当函数发生 panic 时，它会终止运行，在执行完所有的延迟函数后，程序控制返回到该函数的调用方。这种过程会一直持续下去，直到当前协程的所有函数都返回退出，然后程序会打印出 panic 信息，接着打印出堆栈跟踪，最后程序终止。**

### recover

recover 是一个内建函数，用于重新获得 panic 协程的控制。

**只有在延迟函数内部，调用 recover 才有用。在延迟函数内调用 recover，可以取到 panic 的错误信息，并且停止 panic 续发事件，程序运行恢复正常。**

**只有在相同的 Go 协程中调用 recover 才管用。recover 不能恢复一个不同协程的 panic**

### make

make 的作用是初始化内置的数据结构，如：切片、map、channel 等

### new

new 的作用是根据传入的类型在堆上分配一片内存空间并返回指向这片内存空间的指针