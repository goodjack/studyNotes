## import "C"

`import "C"` 表示使用 CGO 特性，紧跟在这行语句前面的注释是一种特殊语法，里面包含正常的 C 语言代码。

```go
/*
#include <stdio.h>

void printint(int v) {
	printf("printint:%d\n",v);
}
*/
import "C"

func main() {
  v := 42
  C.printint(C.int(v))
}
```

`import "C"` 语句前的注释中可以通过 `#cgo` 语句设置编译阶段和链接阶段的相关参数。

- 编译阶段的参数主要用于定义相关宏和指定头文件检索路径，CFLAGS
- 链接阶段的参数主要指定库文件检索路径和要链接的库文件，LDFLAGS

```go
//#cgo CFLAGS: -DPNG_DEBUG=1 -I./include
//#cgo LDFLAGS: -L/usr/local/lib -lpng
//#include <png.h>
import "C"
```

`-D`：定义宏 PNG_DEBUG 值为 1

`-I`：定义头文件包含的检索目录，c 头文件检索可以是相对路径

`-L`：指定了链接时库文件检索目录，库文件检索必须是绝对路径

`-l`：指定链接时需要链接 png 库

