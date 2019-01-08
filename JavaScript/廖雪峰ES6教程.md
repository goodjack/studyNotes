strict 模式(所有的js代码都应该使用这个模式)

必须使用var声明变量，未使用会报错

'use strict';

反引号表示多行字符串

模板字符串

var mes = `你好，${name}，你今年${age}岁了`;

in判断一个属性存在，有可能这个属性不属于它，有可能是继承，要判断一个属性是自身拥有而不是继承，用hasOwnProperty()

Javascript中存在Truthy和Falsy概念，除了Boolean值true，false外，所有类型的Javascript均可用于逻辑判断：

1、所有的Falsy值，当进行逻辑判断时均为false。Falsy值包括：false、undefined、null、正负0、NaN、‘ ’。

2、其余所有值均为Truthy，当进行逻辑判断时均为true。注意Infinity、空数组、“0”都是Truthy

函数接收的参数形式

arguments关键字，只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数

rest参数只能写在最后，前面用...标识，多余的参数以数组形式交给变量rest。如果传入的参数连正常定义的参数都没填满，也不要紧，rest参数会接收一个空数组（注意不是undefined）。

JavaScript的函数在查找变量时从自身函数定义开始，从“内”向“外”查找。如果内部函数定义了与外部函数重名的变量，则内部函数的变量将“屏蔽”外部函数的变量。

JavaScript默认有一个全局对象Window

JavaScript实际上只有一个全局作用域。任何变量（函数也视为变量），如果没有在当前函数作用域中找到，就会继续往上查找，最后如果在全局作用域中也没有找到，则报ReferenceError错误。

名字空间 例： `var MYAPP={}; MYAPP.name='myaapp'; ` 这样可以避免冲突

`for(let a in s)` 这个循环出了下标	

`for(let a of s)` 循环出了值	

不同之处是for.......in 会把额外的值也循环出来例：

a.name = 'hello',for.....in 会把name这个下标也循环出来，for.......of 不会

在for循环等语句块中是无法定义具有局部作用域的变量的，例：

```javascript
function foo() {     
    for (var i=0; i<100; i++) {         
        //     
    }     
    i += 100; // 仍然可以引用变量i 
}

//可以改成
function foo() {     
    var sum = 0;     
    for (let i=0; i<100; i++) {         
        sum += i;     
    }     
    // SyntaxError:     
    i += 1; 
}

```

函数方法apply和call的区别：

apply()把参数打包成Array再传入；

call()把参数按顺序传入。

apply 和 call 方法的第一个参数如果不传入对象，传入null，此时的 this 指向 window，如果传入对象，this 指向该对象

`Math.max.apply(null, [3, 5, 4]); // 5 ` 

`Math.max.call(null, 3, 5, 4); // 5`

对普通函数调用，我们通常把this绑定为null。

```javascript
function string2int(s){

    return +s; //隐式转换，字符串变成number

}

console.log(string2int('12345'));

```

匿名函数应该这样写 `(function (x) {     return x * x; })(3);`

箭头函数=> 也是匿名函数

生成器定义 `function* xxx()`

Date

 JavaScript的Date对象月份值从0开始，牢记0=1月，1=2月，2=3月，……，11=12月。

使用Date.parse()时传入的字符串使用实际月份01~12，转换为Date对象后getMonth()获取的月份值为0~11。

jq扩展原则：

给$.fn绑定函数，实现插件的代码逻辑；

插件函数最后要return this;以支持链式调用；

插件函数要有默认值，绑定在 `$.fn.<pluginName>.defaults` 上；

用户在调用时可传入设定值以便覆盖默认值。