```go
// 这种写法应该写到 _test.go 中，不应该被编译
var _ I = T{}
var _ I = &T{}

func TestSomeTypeImplementSomeInterface(t *testing.T) {
    var i interface{} = new(SomeType)
    if _,ok := i.(SomeInterface); !ok {
        t.Fatalf("expected %T to implement SomeInterface",i)
    }
}
```

上面写法代表着，判断结构`T` 是否实现了 `I` ，使用一个 `_` 来接收这个实例类型就可以避免 `no used` 错误