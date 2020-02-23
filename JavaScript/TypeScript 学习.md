## 基础类型

```typescript
let isDone: boolean = false; // boolean 类型
let hex: number = 0xf00d; // number 类型
let color: string = "blue"; // string 类型
let list: number[] = [1,2,3]; // array 类型，number[] 表示 此数组全是 number 类型
let list:Array<number> = [1,2,3]; // 数组泛型，Array<元素类型>
let x: [string,number]; // tuple 类型，必须按照顺序赋值
enum Color {Red,Green,Blue} // enum 类型
let notSure: any = 4;
function warnUser(): void {} // 可以赋值 undefined 和 null，如果开启 --strictNullChecks void 只允许赋值 null

// unionType 
let a: string|number|boolean = "fasf";

// never 类型是表示那些用不存在的值得类型，never 是那些总会抛出异常或根本就不会有返回值的函数的返回值类型。
// never 是任何类型的子类型，也可以赋值给任何类型，没有任何类型是 never 的子类型，即使是 any 也不可以赋值给 never
function error(message:string):never{
  throw new Error(message);
}

// object 类型
declare function create(o: object|null):void; // 只能是 object 或 null

// 类型断言
let strLength: number = (<string>someValue).length;
let strLength: number = (someValue as string).length; // jsx 只支持这种语法
```

#### 泛型

泛型，因为许多算法和数据结构并不会依赖于对象的实际类型。表示接受任何一个类型，会返回同样的类型。

泛型常用`T、U、V` 表示

```typescript
function reverse<T>(items:T[]):T[] {}
```

上面的函数表示接收到的类型和返回类型是一样的，如：`string[]或 number[]` 传入则返回对应的类型。

```typescript
class Queue<T> {
  private data:T[] = [];
  push = (item:T) => this.data.push(item);
  pop = ():T|undefined => this.data.shift();
}

// 实例化了一个接收 number 类型的队列，<type> 可以使任何定义过的类型
const queue = new Queue<number>();
```



#### 交叉类型

交叉类型，可以从两个对象中创建一个新对象，这个对象拥有两个对象的所有功能。

```typescript
function extend<T,U>(first:T,second:U):T&U {
  const result = <T&U>{};
  
  for(let id in first){
    (<T>result)[id] = first[id];
  }
  
  for(let id in second) {
    if(!result.hasOwnProperty(id)) {
      (<U>result)[id] = second[id];
    }
  }
  
  return result;
}

// 此时 x 拥有了 a 属性与 b 属性
const x = extend({a:'hello'},{b:43});
```

## @types

`.d.ts` 文件是一个声明文件，如给 jQuery 定义一个声明文件

```typescript
declare type JQuery = any;
declare const $:JQuery;
```



[DefinitelyTyped]([DefinitelyTyped](https://github.com/borisyankov/DefinitelyTyped)) 记录了绝大部分的 JavaScript 库

全局 @types，可以使用 tsconfig.json 控制

```json
{
  "compilerOptions":{
    "types":[
      "jquery"
    ]
  }
}
```

## 类型断言

typescript 允许覆盖它的判断，能以任何想要的方式去分析它，这种机制被称为类型断言。

```typescript
interface Foo {
  bar:number;
  bas:string;
}

const foo = {} as Foo // 将{}推断为 Foo 类型

let foo:any;
let bar = <string>foo;	// 这也是一种类型断言，但是不提倡会在 JSX 中发生冲突
```

断言与类型转换的区别：

​	类型转换是某种运行时的支持，类型断言则是编译时

## Interface 类型检测

#### Optional Properties 可选属性

```typescript
interface SquareConfig {
    color?: string;
    width?: number; // 带上?表示此类型检查是可选的
}
											// 表示对该对象的返回类型进行检查
function createSquare(config: SquareConfig): {color:string;area: number} {
    ...
}
```

#### Readonly properties 只读属性

```typescript
interface Poin {
    readonly x: number;
    readonly y: number;
}

const p1: Point = {x:10,y:20};
p1.x = 5 // error,属性只读
const ro: ReadonlyArray<number> = [1,2,3,4]; // 表示所有数组的类型都是只读的且是 number 类型
```

readyonly 和 const，变量使用 const ，属性使用 readonly。

#### Function Types 函数类型检测

```typescript
interface SearchFunc {
    (source: string,subString: string): boolean; // 表示该方法返回 boolean
}
let mySearch:SearchFunc;
mySearch = function(source: string,subString: string) {
  	...
    return true;
}
```

#### Indexable Type 索引类型检测

```typescript
// 可索引类型
interface StringArray {
    [index: number]: string; // 表示是一个索引类型的检测，索引类型必须为 number，值必须是 string
}

let myArray: StringArray;
myArray = ["Bob","Fred"];
let myStr: string = myArray[0];
```

#### Class Types 类的类型检测

类静态部分与实例部分的区别：当一个类实现了一个接口时，只对其实例部分进行类型检查。constructor 存在于类的静态部分，所以不在检查范围内。

```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date;
}


// 定义 clock 静态部分的接口
interface ClockContructor {
  new (hour:number,minute:number):ClockInterface;
}

// 定义 clock 实例部分的接口
interface ClockInterface {
  tick();
}

// 使用一个函数去创建这个实例
function createClock(ctor:ClockConstructor,hour:number,minute:number):ClockInterface {
  return new ctor(hour,minute);
}

class DigitalClock implements ClockInterface {
  constructor(h:number,m:number){}
  tick() {
    console.log("111");
  }
}

let digtital = createClock(DigitalClock,12,17); // 在 createClock 函数中会检查 DigitalClock 是否符合构造函数签名
```

#### Extending Interfaces 扩展interface

```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}
```

