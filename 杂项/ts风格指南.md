

# 基础风格

#### 变量和函数

使用驼峰（camelCase）命名变量和函数名

```tsx
// Bad
var FooVar;
function BarFunc() { }

// Good
var fooVar;
function barFunc() { }
```

#### 类

使用帕斯卡（PascalCase）命名类名

```tsx
// Bad
class foo { }

// Good
class Foo { }
```

使用帕斯卡（PascalCase）命名类成员与方法

```tsx
// Bad
class Foo {
    Bar: number;
    Baz() { }
}

// Good
class Foo {
    bar: number;
    baz() { }
}
```

#### 接口

- 使用帕斯卡（PascalCase）命名接口
- 使用驼峰（camelCase）命令成员
- 误使用 `I` 前缀

```tsx
// Bad
interface IFoo { }

// Good
interface Foo { }
```

#### 枚举

使用帕斯卡（PascalCase）命名枚举

```tsx
// Bad
enum color { }

// Good
enum Color { }
```

使用帕斯卡（PascalCase）命名枚举成员

```tsx
// Bad
enum Color {
    red
}

// Good
enum Color {
    Red
}
```

#### Null vs Undefined

最好不要显式使用不可用的值


```tsx
// Bad
let foo = { x:123, y:undefined };

// Good
let foo: { x:number, y?:number } = { x:123 };
```

通常使用 undefined（ 而不是返回一个类似于 { valid:boolean, value?:Foo } 的对象 ）

```tsx
// Bad
return null;

// Good
return undefined;
```

参考 Node.js 回调函数 Error First 风格（若未发生异常，error 参数值设置为 null）

```tsx
// Bad
cb(undefined)

// Good
cb(null)
```

避免使用值比较判断对象是否为 null 或 undefined


```tsx
// Bad
if (error === null)

// Good
if (error)
```

#### 数组

声明数组时使用 `foos:Foo[]` 而不是 `foos:Array<Foo>`，便于阅读

#### 类型 vs 接口

当你需要复合类型时，使用 type

```tsx
type Foo = number | { someProperty: number }
```

当你需要继承或实现时，使用 interface

```tsx
interface Foo {
  foo: string;
}

interface FooBar extends Foo {
  bar: string;
}

class X implements FooBar {
  foo: string;
  bar: string;
}
```

# 普通类型

## `Number`，`String`，`Boolean`和`Object`

*不要*使用如下类型`Number`，`String`，`Boolean`或`Object`。 这些类型指的是非原始的装盒对象，它们几乎没在JavaScript代码里正确地使用过。

```ts
/* 错误 */
function reverse(s: String): String;
```

*应该*使用类型`number`，`string`，and `boolean`。

```ts
/* OK */
function reverse(s: string): string;
```

使用非原始的`object`类型来代替`Object`

## 回调函数返回值类型

*不要*为返回值被忽略的回调函数设置一个`any`类型的返回值类型：

```tsx
/* bad */
function fn(x: () => any) {
    x();
}
```

*应该*给返回值被忽略的回调函数设置`void`类型的返回值类型：

```tsx
/* OK */
function fn(x: () => void) {
    x();
}
```

*为什么*：使用`void`相对安全，因为它防止了你不小心使用`x`的返回值：

```tsx
function fn(x: () => void) {
    var k = x(); // 如果不小心使用了回调返回值
    k.doSomething(); // 如果返回的是 any 类型，这里就不会报错了
}
```

## 回调函数里的可选参数

*不要*在回调函数里使用可选参数除非你真的要这么做：

```tsx
/* bad */
interface Fetcher {
    getObject(done: (data: any, elapsedTime?: number) => void): void;
}
```

这里有一种特殊的意义：`done`回调函数可能以1个参数或2个参数调用。 代码大概的意思是说这个回调函数不在乎是否有`elapsedTime`参数， 但是其实不需要把这个参数当成可选参数来达到此目的 – 因为总是允许提供一个接收较少参数的回调函数。

*应该*写出回调函数的非可选参数：

```tsx
/* OK */
interface Fetcher {
    getObject(done: (data: any, elapsedTime: number) => void): void;
}
```

就和调用数组里的很多方法一样，回调很多情况下是不会用所有的参数的

```tsx
var arr = [1, 2, 3]
arr.map((item) => console.log(item)) //  这里没有使用到参数 index 和 arr 也是不会报错的
```

## 重载与回调函数

*不要*因为回调函数参数个数不同而写不同的重载：

```ts
/* bad */
declare function beforeAll(action: () => void, timeout?: number): void;
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
```

*应该*只使用最大参数个数写一个重载：

```ts
/* OK */
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
```

原因：回调函数总是可以忽略某个参数的，因此没必要为参数少的情况写重载。 参数少的回调函数首先允许错误类型的函数被传入，因为它们匹配第一个重载。

# 函数重载

## 顺序

*不要*把一般的重载放在精确的重载前面：

```ts
/* bad */
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: any, wat?
```

*应该*排序重载令精确的排在一般的之前：

```ts
/* OK */
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: string, :)
```

原因：TypeScript会选择*第一个匹配到的重载*当解析函数调用的时候。 当前面的重载比后面的“普通”，那么后面的被隐藏了不会被调用。



## 使用联合类型

*不要*为仅在某个位置上的参数类型不同的情况下定义重载：

```ts
/* bad */
interface Moment {
    utcOffset(): number;
    utcOffset(b: number): Moment;
    utcOffset(b: string): Moment;
}
```

*应该*尽可能地使用类型类型：

```ts
/* OK */
interface Moment {
    utcOffset(): number;
    utcOffset(b: number|string): Moment;
}
```

注意我们没有让`b`成为可选的，因为签名的返回值类型不同。

*为什么*：This is important for people who are “passing through” a value to your function:

```ts
function fn(x: string): void;
function fn(x: number): void;
function fn(x: number|string) {
    // When written with separate overloads, incorrectly an error
    // When written with union types, correctly OK
    return moment().utcOffset(x);
}
```

