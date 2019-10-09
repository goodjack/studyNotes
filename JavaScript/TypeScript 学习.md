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

