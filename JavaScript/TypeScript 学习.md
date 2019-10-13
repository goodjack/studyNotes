## Basic Types

```typescript
let isDone: boolean = false; // boolean 类型
let hex: number = 0xf00d; // number 类型
let color: string = "blue"; // string 类型
let list: number[] = [1,2,3]; // array 类型，number[] 表示 此数组全是 number 类型
let x: [string,number]; // tuple 类型，必须按照顺序赋值
enum Color {Red,Green,Blue} // enum 类型
let notSure: any = 4;
function warnUser(): void {} // 如果开启 --strictNullChecks void 只允许赋值 null

// unionType 
let a: string|number|boolean = "fasf";

// never 类型不太理解

// object 类型
declare function create(o: object|null):void; // 只能是 object 或 null

// 类型断言
let strLength: number = (<string>someValue).length;
let strLength: number = (someValue as string).length; // jsx 只支持这种语法
```

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
interface StringArray {
    [index: number]: string; // 表示是一个索引类型的检测，索引必须为 number，值必须是 string
}

let myArray: StringArray;
myArray = ["Bob","Fred"];
let myStr: string = myArray[0];
```

#### Class Types 类的类型检测

```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date;
}
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

