## 数字

JavaScript 的算数运算在溢出（overflow）、下溢（underflow）或被零整除时不会报错。当数字运算结果超过了 JavaScript 所能表示的数字上限（溢出），结果为一个特殊的无穷大（infinity）值，为负无穷大时使用（-infinity）表示。

**浮点数计算舍入误差，多数语言都会存在的问题**

```javascript
const a = 0.3 - 0.2;

const b = 0.2 - 0.1;

console.log(a === b); # false
console.log(a === 0.1); # false 
console.log(b === 0.1); # true

# 一般这种精度是足够了，在针对一些金融计算时，使用大整数“分”而不使用小数“元”进行运算可以避免精度丢失问题
```



## 字符串

字符串是一组由 16 位值组成的不可变的有序序列，每个字符通常来自于 Unicode 字符集。

JavaScript 采用 UTF-16 编码的 Unicode 字符集，JavaScript 字符串是由一组无符号的 16 位值组成的序列。最常用的 Unicode 字符，都是通过 16 位的内码表示，并代表字符串中的单个字符。那些不能用 16 位的 Unicode 字符表示的则遵循 UTF-16 编码规则（用两个 16 位值组成的一个序列来表示）。这意味着一个长度为 2 的 JavaScript 字符串（两个 16 位值）有可能表示一个 Unicode 字符。



## 数组

对象不能直接操作数组的方法，但可以间接的调用数组方法。

```javascript
const a = {0,1,2,length:3};
Array.prototype.join.call(a,'+');
# 在一些浏览器上直接将 Array 构造函数上直接定义了函数
Array.join(a,'+');
# 不是所有浏览器都支持，兼容写法
Array.join = Array.join || function (a,sep){
 return Array.prototype.join.call(a,sep);   
};
```



## 函数

#### 闭包

> 每次调用 JavaScript 函数时，都会创建一个新的对象用来保存局部变量，把这个对象添加至作用域链。
>
> 当函数返回时，就从作用域链中将这个绑定变量的对象删除。
>
> 如果不存在嵌套函数，也没有其他引用指向这个绑定对象，就会被当做垃圾回收掉。
>
> 如果定义了嵌套函数，，每个嵌套函数都各自对应一个作用域链，并且这个作用域链指向一个变量绑定对象。如果这些嵌套的函数对象在外部函数中保存下来，那么它们也会和所指向的变量绑定对象一样被当做垃圾回收。
>
> 如果定义的嵌套函数，将它作为返回值返回或存储在某处的属性里，这是就会有一个外部引用指向这个嵌套函数，并且它所指向的变量绑定对象也不会被当做垃圾回收。

#### 柯里化

柯里化定义：将一个多参数的函数，转化为单参数函数

```javascript
const sum = function(x,y) {
    return x + y;
};
const succ = sum.bind(null,1);	// 此处将 1 绑定到了 x
succ(2);	// 3

function sum(x){
    return function(y){
        return (x + y);
    }
}

const succ = sum(2);
succ(2); // 4
```

