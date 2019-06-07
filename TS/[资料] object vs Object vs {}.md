# Object VS object VS any VS {}

**以下代码编译结论于 TypeScript 严格模式下得出**

```json
// tsconfig.json
{
  "compilerOptions": {
    "strictNullChecks": true, // strictNullChecks 需要设置为 true
    ...
  }
  ...
}
```

首先我们来了解一下 TypeScript 中的 object 类型。

在 TypeScript 2.2 版本之前没有一个类型用于表述「非原始数据类型」，即一个值非 number、string、boolean、symbol、null 或 undefined，所以在 2.2 版本的 TypeScript 中加入了 object 类型（注意是首字母小写的 object, 而不是 Object）
那么新加入的 object 与 Object 或 {} 有什么区别呢？
带着这个疑问，我们通过官方示例代码来了解 object 的用法

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

从上述代码就能比较直观地感受到 object 类型「非原始数据类型」的特性。

## Object VS object

基于上述代码我们用 Object 类型来做一个对比：

```ts
declare function create1(o: object): void;
declare function create2(o: Object): void;

create1({ prop: 0 }); // OK
create1(null); // Error
create1(undefined); // Error
create1(42); // Error
create1("string"); // Error
create1(false); // Error

create2({ prop: 0 }); // OK
create2(null); // Error
create2(undefined); // Error
create2(42); // Ok
create2("string"); // Ok
create2(false); // Ok
```

从上述代码我们就能直观地感受 Object 与 object 之间的区别，即 Object 可以被推论为 number、string、boolean 这三个原始数据类型。

## Object VS any (非严格模式)

由于所有类型都继承自 Object ，所以我们有时候可能会困惑于以下两种变量声明方式：

```ts
let x: any;
let y: Object;

x = "hello word";
x = 123;
x = null;
x = {};
x = undefined;
x = function() {};

y = "hello word";
y = 123;
y = null; // 为了进行对比，此处是非严格模式，严格模式下无法通过
y = {};
y = undefined; // 为了进行对比，此处是非严格模式，严格模式下无法通过
y = function() {};
```

上述代码能够通过编译，但是实际上它们之间是存在区别的，any 没有接口（interface）而 Object 有 Object interface，所以 any 比 Object 更加自由，我们来看下面一段代码：

```ts
let a: any;
let b: Object;
a.doSomething(); // OK, 编译器会信任你的用法
b.doSomething(); // Error , b 中未声明 doSomething 方法
```

## Object VS {}

{} 继承于 Object（所有类型都继承自 Object），但 Object 与 {} 在 TypeScript 中的用法是等价的。