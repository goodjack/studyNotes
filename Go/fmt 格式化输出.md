```go
type Test struct {
    a int
    b int
}

func main() {
    test := &Test{12,10}
}
```

`%T`  会输出类型的完全规格

`fmt.Printf("test is %T",test)`：`test is *main.Test`

`%#v` 会给出实例的完整输出，包括它的字段

`fmt.Printf("test is %#v",test)`：`test is &main.Test{a:12,b:10}`

