## PHP 错误和异常处理

#### PHP 错误相关函数

- `ini_set('display_errors',0);`	//关闭错误输出
- `error_reporting(E_ALL&~E_NOTICE);` //设置错误报告级别
- `ini_set('error_reporting',0);` // 设置错误报告级别

#### PHP 异常与错误的抛出

- 异常抛出：`throw new Exception('some error message');`
- 错误抛出：`trigger_error()`
- `trigger_error()` 触发的错误不会被 `try-catch()` 异常捕获语句捕获

#### PHP 错误处理

- `set_error_handler()`

只能处理 `Deprecated、Notice、Waring` 这三种错误，脚本继续执行后发生错误的后一行

- `register_shutdown_function()`

脚本结束前的最后一个回调函数，无论是 die，错误，异常 还是脚本正常结束都会调用

#### PHP 异常处理

- `set_exception_handler()`

设置默认的异常处理程序，有 try/catch 捕获的话这个函数就不会执行

set_exception_handler() ，不仅可以接受函数名，还可以接受类的方法（公开的静态及非静态方法）

#### PHP7 异常处理变化

PHP7 之后，出现一个异常和错误通用接口 Throwable，Exception 和 Error 类都实现了该接口，使得 Error 或 Error 的派生类的错误对象（大部分 Fatel Error）也可以像 Exception 一样被捕获

```php
try{
    // code
} catch(Exception $e){
    // 捕获异常
}catch(Error $error){
    // 捕获错误
}
//或者
try{
    // code
}catch(Throwable $e) {
    // 可以捕获异常和错误
}
```

