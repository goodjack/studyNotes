go module 生成的目录下会出现两个文件

- `go.mod` 声明模块名称、go的版本、该模块的依赖信息
- `go.sum` 该模块的所有依赖项的校验和。go 在编译前会下载并检查校验和，如果不一致将会拒绝编译。

go module 相关的环境变量

```
GOPROXY="https://proxy.golang.org,direct"
GOSUMDB="gosum.io+ce6e7565+AY5qEHUk/qmHc5btzW45JVoENfazw8LielDsaI+lEbq6"
GOPRIVAT="XXX"
GONOPROXY="XXX"
GONOSUNDB="XXX"
```

- `GOPROXY` 指示镜像递增和获取顺序，每个镜像URL之间用 `,` 分隔，按照优先顺序排列。direct 表示源站。
  - `GOPROXY=off` 表示禁止从互联网下载任何依赖
  - `GOPROXY=direct` 表示依赖一律从源站下载
- `GOSUMDB` 指示校验和服务器的地址和公钥，对于地址 `sum.golang.org`，公钥可以省略（内置）
- `GOPRIVATE` 表示私域前缀。私域前缀下的所有依赖一律从源站下载，而且不做校验。支持多个私域前缀，每个私域用 `,` 分隔。支持通配符 `*`
- 

go 包管理工具：

- `go mod init xxx` 创建一个新的模块，初始化 `go.mod` 并生成相应的描述
- `go build,go test` 会在需要的时候在 `go.mod` 文件中添加新的依赖项
- `go list -m all` 列出当前模块的所有依赖项
- `go get` 修改制定依赖项的版本或者添加一个新的依赖项
- `go mod tidy` 移除模块中没有用到的依赖项

### 将 exported 类型变为其它 package 不可访问

通过定义一个 `internal` 包，将其它包中的 `exported` 函数、类型放在 `internal` 包下这样，就只会被其父级访问：

`/a/b/c/internal/d/e/f` 可以被 `/a/b/c` 导入，不能被 `/a/b/g` 导入

### 访问其它包中的私有方法

对外声明一个导出函数 `external function`，例如：`time.go 中的 time.Sleep` 这个函数就是在 `time` 包内进行函数声明，实现是在 runtime 中的一个 unexported 函数中（`runtime.timeSleep`) 实现的 。

将相关的函数定义在 runtime 包中的好处是，可以访问 runtime 包中 unexported 的类型，同时相关的函数还可以被其它包访问。

使用 go 提供的一个指令 `//go:linkname localname importpath.name`，这个指令为函数或者变量 `localname` 使用 `importpath` 作为目标文件的符号名。**因为这个指令破坏了类型系统和包的模块化，所以它只能在 `import unsafe` 的情况下才能使用**

`importpath.name`格式：`/a/b/c/d/pkg.foo` ，此时在 `/a/b/c/d/pkg` 包中就可以使用这个函数 `foo`



