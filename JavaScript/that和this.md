**非严格模式下：**
```javascript
var xiaoming = {
    name:'小明',
    birth:1990,
    age:function(){
        var y = new Date().getFullYear();
        return y-this.birth;	//非严格模式下，这个this指向了小明这个对象
    }
};
xiaoming.age;	//function.xiaoming.age()
xiaoming.age();	//今年调用是25，明年调用就变成26
```

拆开写的话：

```javascript
function getAge(){
    var y = new Date().getFullYear();
    return y -this.birth;	//this指向了window
}

var xiaoming = {
    name:'小明',
    birth:1990,
    age:getAge
};
xiaoming.age();	//25,正常结果
getAge();	//NaN
```

```javascript
var fn = xiaoming.age;	//拿到xiaoming的age函数
fn();	//NaN,这个函数里的this也是指向了window
```
**严格模式下：**
```javascript
'use strict';
var xiaoming = {
    name:'小明',
    birth:1990,
    age:function(){
        function getAgeFromBirth(){
            var y = new.Date().getFullYear();
            return y-this.birth;
        }
        return getAgeFromBirth();
    }
}
xiaoming.age();	//Uncaught TypeError: Cannot read property 'birth' of undefined

```

上面的代码，`this`指针只在`age`方法的函数内指向`xiaoming`，在函数内部定义的函数，`this`又指向了`undefined`，在非strict模式下，它重新指向`window`

**修复的办法：**

```javascript
'use strict';
var xiaoming = {
    name:'小明',
    birth:1990,
    age:function(){
        var that = this;	//把this复制给that
        function getAgeFromBirth(){
            var y = new.Date().getFullYear();
            return y-that.birth;
        }
        return getAgeFromBirth();
    }
}

xiaoming.age();	//25
```

apply：指定函数的`this`指向哪个对象，函数本身的方法`apply`，它接受两个参数，第一参数需要绑定`this`变量，第二个参数是`array`，表示函数本身的参数

用`apply`修复`getAge()`调用：

```javascript
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25
getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空
```

call和apply的区别：

- `apply`把参数打包成`array`再传入
- `call`把参数按顺序传入

```javascript
Math.max.apply(null,[3,5,4]);
Math.max.call(null,3,5,4);
//对普通函数调用通常把this绑定为null
```



箭头函数和匿名函数的区别是：箭头函数内部的`this`是词法作用域，由上下文确定

```javascript
var obj = {
    birth: 1990,
    getAge: function () {
        var b = this.birth; // 1990
        var fn = function () {
            return new Date().getFullYear() - this.birth; // this指向window或undefined
        };
        return fn();
    }
};
```

现在，箭头函数完全修复了`this`的指向，`this`总是指向词法作用域，也就是外层调用者`obj`： 

```javascript
var obj = {
    birth: 1990,
    getAge: function () {
        var b = this.birth; // 1990
        var fn = () => new Date().getFullYear() - this.birth; // this指向obj对象
        return fn();
    }
};
obj.getAge(); // 25
```

如果使用箭头函数，以前的那种`var that = this;`就不再需要了

由于`this`在箭头函数中已经按照词法作用域绑定了，所以，用`call()`或者`apply()`调用箭头函数时，无法对`this`进行绑定，即传入的第一个参数被忽略： 

```javascript
var obj = {
    birth: 1990,
    getAge: function (year) {
        var b = this.birth; // 1990
        var fn = (y) => y - this.birth; // this.birth仍是1990
        return fn.call({birth:2000}, year);
    }
};
obj.getAge(2015); // 25
```

参考链接：

[廖雪峰-JavaScript-变量作用域与解构赋值](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014344993159773a464f34e1724700a6d5dd9e235ceb7c000)

[廖雪峰-JavaScript-箭头函数](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001438565969057627e5435793645b7acaee3b6869d1374000)

