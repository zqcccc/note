# Typescript

## 内容概要

- 强弱类型
- 静态类型与动态类型
- JavaScript 自有类型系统的问题
- Flow 静态类型检查方案
- Typescript 语言规范与基本使用

## 类型系统

常用来区分语言的两个维度

- 强类型与弱类型（类型安全）
- 静态类型与动态类型（类型检查）

### 强弱类型

强弱类型其实也没有一个官方说话，比较靠谱的说法是，强类型在语言层面限制函数的实参类型必须与形参类型相同的就是强类型，弱类型则没有这样的限制，运行可以有问题，但语法上没有问题

很多人在理解上也不一样，但可以确定的是，强类型有更强的类型约束，而弱类型中几乎没有什么约束。

强类型语言中不允许任意的隐式类型转换，而弱类型语言则允许任意的数据隐式类型转换

强类型在语言层面就做了函数实参类型和形参类型相同的限制，在编译的时候就可以报错，弱类型需要等到运行的时候通过逻辑判断去限制

这种定义下，Python 就是强类型，JavaScript 就是弱类型

*变量类型允许随时改变的特点，不是强弱类型的差异*

### 静态类型与动态类型

这里的定义没什么争议

一个变量声明时它的类型就是明确的，且声明过后，它的类型就不允许再修改的就是静态类型

运行阶段才能够明确变量类型，而且变量的类型随时可以改变

也可以说，动态类型语言中的变量是没有类型的，变量中存放的值是有类型的，JavaScript 就是标准的动态类型语言

## JavaScript 类型系统特征

**弱类型**且**动态类型**，任性

十分灵活多变的同时，就会缺失类型系统的可靠性，不靠谱

为什么 JavaScript 不是强类型且静态类型呢？

早前的 JavaScript 应用简单，可能几百行甚至几十行代码就完事了，JavaScript 作为一种脚本语言，没有编译环节，即使设计成静态类型的语言也没有什么意义，因为静态类型语言需要在编译阶段去做类型检查

早前灵活多变是一种优势，而现在 JavaScript 已经不是最开始的样子了，现在的 JavaScript 随处可见十分庞大的应用，代码变得越来越复杂，开发周期也越来越长。

在现在的大规模应用下，早前的优势反而变成了短板

## 弱类型的问题

```js
const obj = {};
obj.foo(); // 方法就根本不存在

// 这里期望是数字，传入字符串就会有意外
function sum(a, b) {
  return a + b;
}
console.log(sum(100, 100)); // 200
console.log(sum(100, "100")); // 100100

// obj 的 key 只能是 string 或者 symbol
const obj = {};
obj[true] = 100;
console.log(obj["true"]); // 100
```

这个代码在语法层面没有任何问题，要运行才会报错

如果是强类型的语言直接在编译就会报错了

弱类型只能约定，君子约定有隐患，强制要求有保障

## 强类型的优势

- 错误更早暴露
- 代码更加智能，编码更加准确（ide 能为我们开发者做更多事）
- 重构更牢靠（ide）
- 减少不必要的类型判断

## Flow

JavaScript 的类型检查器

类型注解

```js
// @flow // 这个注释不能省
function sum(a: number, b: number) { // 形参后面的就是类型注释
  return a + b;
}
```

flow 就是一个 npm 模块，安装了模块，就可以用 flow 去编译检测代码中的问题，flow 写的类型注解不是标准的 JavaScript 代码，所以还也用 `flow-remove-types` 去把注解删除，可以把源码都放在 src 目录下，然后 `yarn flow-remove-types src -d dist` 去把正常的 js 代码生成到 dist 目录下，以上都是官方的方法

flow 就是在加了一个代码编译的过程，babel 也是常用来编译代码的一个模块，flow自然也可以和 babel 结合使用

安装 `@babel/core @babel/cli @babel/preset-flow` 这三个包

然后在根目录创建 `.babelrc` 文件

```json
{
  "preset": "@babel/preset-flow"
}
```

执行 `yarn babel src -d dist` 就可以用 babel 去编译转换文件了

### 开发工具插件

上面讲的方法都是每回去命令行去输入命令才能知道编译是不是正常，如果能在开发工具（VSCode）上就给出提示当然会是更好的体验，在 VSCode 的拓展市场里，`Flow Language Support` 就是为这个而生的

不过这个插件是每回保存文件才去检查的，可能有点迟钝

[flow 各编辑器支持插件](https://flow.org/en/docs/editors/)

### 类型推断 Type Inference

```js
function square(n) { // 这里不加注释也可以从后面的 n*n 推断出 n 是数字
  return n * n;
}
square("100"); // ide 报错
```

即便 flow 有类型推断，还是建议写类型注解

### 类型注解 Type Annotations

就是在变量后面加 `: type` 来指明类型

### 原始类型 Primitive Types

- string
- number
  - NaN
  - Infinity
  - 普通数字
- boolean
- null
- void
  - undefined
- symbol

### 数组类型 Array Types

需要指定泛型参数 `const arr: Array<number> = [1, 2, 3]`

或者这样写 `cosnt arr: number[] = [1, 2, 3]`

还能像对象字面量一样 `const foo: [string, number] = ['foo', 100]` 这样的就只能是两位元素的固定长度数组，应该叫元组

### 对象类型 Object Types

```js
const obj1: { foo: string, bar: number } = {
  foo: 'string',
  bar: 100,
}
const obj1: { foo?: string, bar: number } = {
  bar: 100,
}
const obj1: { [string]: string } = {
  foo: 'string',
}
```

### 函数类型 Function Types

```js
function foo (callback: (string, number) => void) {
  callback('string', 100)
}
```

### 特殊类型

字面量类型

```js
const a: 'foo' = 'foo' // 只能是 'foo'

const type: 'success' | 'warning' | 'danger' = 'success' // 或类型

const b: string | number = 'string'

type StringOrNumber = string | number
const c: StringOrNumber = 'string'

const gender: ?number = null // 等于下一个
const gender: number | null | void = null // maybe 类型
```

### Mixed & Any

```js
// mixed 就是所有的类型，any 也是一样
// 但 mixed 是强类型，any 是弱类型
function passMixed (value: mixed) {
  value.substr(1) // 直接提示错误
  value * value // 直接提示错误
  if （typeof value === 'string') {
    value.substr(1) // 这里就正确了，需要先确定一下类型才能当成那个类型使用
  }
  if （typeof value === 'number') {
    value * value // 这里就正确了，需要先确定一下类型才能当成那个类型使用
  }
}
function passAny (value: any) { // 编译不会报错，运行就不一定了
  value.substr(1)
  value * value
}
```

在实际使用中，尽量不要使用 any 类型，any 存在的意义可能是为了兼容一些老代码

学习 flow 就是为了能看懂 react 和 vue 这类使用了 flow 的库，如果有上面没有出现的类型再[查文档](https://flow.org/en/docs/types/)就行了

[saltycrane flow 类型手册](https://www.saltycrane.com/cheat-sheets/flow-type/latest/) 也是不错的 flow 文档

因为 js 运行总是有一个环境的，而这些环境有很多 api，浏览器有 bom 和 dom，node 有各种模块，这些环境都是对类型有要求的，所以 flow 官方也是有维护这些类型的，仓库地址 https://github.com/facebook/flow/tree/master/lib

## Typescript

JavaScript 的超集（superset），比 JavaScript 多的就是一套类型系统，还有对 ECMAScript 新特性标准的支持，最终执行的时候会被编译成 JavaScript

任何一种 JavaScript 运行环境都支持用 Typescript 去开发

相比于 flow，Typescript 做为一门语言，功能更为强大，生态也更健全、更完善

Typescript 适合开发大型长周期应用

小缺点：

- 语言本身多了很多概念（学习成本，接口，泛型，枚举...）
  - 幸好 Typescript 属于渐进式的
- 项目初期，Typescript 会增加一些成本（不适合小项目）

安装 `yarn add typescript --dev` ，vscode 默认支持 ts 文件的语法检查，写好 ts 文件后，执行 `yarn tsc 文件名` 就可以讲 ts 编译成 js 了

###配置

编译整个项目的时候，一般会有一个配置文件，`yarn tsc --init` 就可以生成 `tsconfig.json` 的配置文件了，如果是整个项目的编译的话，用 `yarn tsc` 命令，要注意配置的源文件目录和生成目录

###原始类型 Primitive Types

跟 flow 差不多

```ts
const a: string = "123";
const b: number = 213;
const c: boolean = true;
// 非严格模式下，以上的变量都是可以为空的
const d: void = undefined; // 严格模式下只能是 undefined，非严格模式下还可以是 null
```

###标准库声明

内置对象类型，如果 `tsconfig.json` 里 `target` 设置的是 `es5`，那就不能用 `ES2015` 里的新对象，用了就报错

解决方法：

- 将 `target` 的 `es5` 改成 `ES2015`
- 如果代码就是要编译成 es5 的话，将 `lib` 配置 ( 数组 ) 加上 `ES2015`，这个时候 `lib` 的配置会覆盖默认的标准库，`console` 这个对象就会报错，`console` 是属于浏览器 BOM 的，`lib` 数组配置里需要再添加 `DOM`，这个 lib 里是包含了 DOM 和 BOM 的

_标准库就是内置对象所对应的声明_

###中文错误消息

`yarn tsc --local zh-CN` 这样可以在命令行编译的时候使用中文的错误消息

VSCode 里是在配置里找到 Typescript 拓展，设置项 local 换成 zh-CN

但其实提示变成中文的话，直接复制中文提示不利于在搜索引擎里去搜索问题，还是用英文好

###作用域

不同文件的相同名字变量的冲突

- 使用 IIFE (Immediately Invoked Function Expression)
- 使用模块导出 `export {...}`

###Typescript object 类型

Typescript 里 `object` 不单指 js 里的普通对象，而是泛指所有的非原始类型

类型 `object` 是全小写的，这种类型的变量，接收对象、数组和函数等都是可以的

```ts
const obj: { a: number } = { a: 1 }; // 这种才是专门接收一个 js Object 对象，但是这对象的属性只能和类型规定的一样，不能多也不能少，不方便使用
// 更好的定义类型应该用接口 interface
```

###Typescript 数组类型

```ts
const arr1: Array<number> = [1, 2, 3];

const arr2: number[] = [1, 2, 3];

function sum(...args: number[]) {
  return args.reduce((prev, current) => prev + current, 0);
}
sum(1, 2, "foo"); // 这里 ’foo‘ 会报错
```

###Typescript 元组

Tuple Types，元组就是明确元素数量和各元素类型的数组，各个元素的类型没必要完全相同

```ts
const tuple: [number, string] = [10, "zzz"];
Object.entries({
  // 这个返回的也是元组
  foo: 123,
  bar: 456
});
```

###Typescript 枚举类型

Enum Types

```ts
enum PostStatus {
  Draft = 0,
  Unpublished = 1,
  Published = 2
}
const post = {
  title: "xxx",
  content: "xxxxxx",
  status: PostStatus.Draft // 类似对象的使用
};
enum PostStatus {
  Draft = 6, // 从 6 开始累加
  Unpublished,
  Published
}
enum PostStatus {
  Draft = "aa", // 字符串的指定要每个都单独指定，其实字符串的枚举并不常用
  Unpublished = "bb",
  Published = "cc"
}
```

Typescript 的很多类型在编译后都会被移除掉，这些类型只是为了在编译过程中可以做类型检查，但是枚举比较特殊，枚举编译完是侵入式的，它编译完会生成一个双向的键值对对象

```js
// 编译完大概是这样的
var PostStatus;
(function (PostStatus) {
  PostStatus[(PostStatus["Draft"] = 0)] = "Draft";
  PostStatus[(PostStatus["Unpublished"] = 1)] = "Unpublished";
  PostStatus[(PostStatus["Published"] = 2)] = "Published";
})(PostStatus || (PostStatus = {}));
```

这样可以通过索引去访问枚举名称；如果我们不需要通过索引去访问枚举的话，我们可以定义常量枚举

```ts
const enum PostStatus {
  Draft,
  Unpublished,
  Published
}
```

常量枚举在编译后就会被移除掉，使用枚举的地方都会被替换成具体的数值，然后后面会跟上枚举名称的注释

###Typescript 函数类型

Function Types，主要是定义输入输出，参数数量和类型要和定义的一致，如果要参数可选就要在参数后面加个问号或者添加 ES6 的默认参数，可选参数或者默认参数都要在参数项的最后，因为参数都是按照位置进行传递的，如果可选参数出现在了必选参数的前面，那必选参数的不能拿到正常的值了，如果要使用任意数量的参数就要用 ES6 的剩余参数

以上说的是函数声明的情况，还有函数表达式的情况

函数表达式可以在变量名后面再接一个类似箭头函数的式子

###Typescript 任意类型

`JSON.stringify` 方法就是可以接受任意类型的参数的，any 类型的值还是动态类型，Typescript 不会去检查 any 类型，扔可以在 any 类型去调用任意的成员，语法上都不会报错，但是执行就没有保障了，any 类型就还有存在类型安全的问题，不要轻易使用 any 类型

###Typescript 隐式类型推断

Type Inference

```ts
let age = 18; // 这里已经被推断成了 number
age = "string"; // 这里就会报错

let foo; // 这种就会被推断成 any
```

即使有隐式类型推断，但还是推荐明确变量的类型

###Typescript 类型断言

Type assertions 是在编译阶段的概念，不是执行阶段的

```ts
const nums = [110, 120, 119, 112]; // 假设这里的数据很可靠
const res = nums.find((i) => i > 0); // 这里的 res 会被推断成 number 或 undefined，ts 认为可能会找不到，而我们认为一定找的到
const square = res * res; // 这里就报错
// 下面用类型断言就不会报错了
const num1 = res as number;
const num2 = <number>res; // JSX 下不能使用
const square = num1 * num1;
```

###Typescript 接口

Interface 就是来约束对象的结构，一个对象如果要实现一个接口就要拥有这个接口所约束的所有成员，interface 在编译后也是会被去除掉的

```ts
interface Post {
  title: string; // string 后面可以加 ,; 也可以什么都不加，风格问题
  content: string;
}
function printPost(post: Post) {
  console.log(post.title);
  console.log(post.content);
}
```

可选成员，只读属性，动态成员

```ts
interface Post {
  subttile?: string;
  subtitle: string | undefined; // 这个和上面那个等价
  readonly summary: string; // 在初始化后这个字段就不能再修改了
}
interface Cache {
  [prop: string]: string;
}
const cache: Cache = {};
cache.foo = "value1";
cache.bar = "value2";
```

###Typescript 类

classes 描述一类具体事物的抽象特征，ES6 以前，函数 + 原型模拟实现类，ES6 开始 JavaScript 中有了专门的 class，TypeScript 增强了 class 的相关语法

```ts
class Person {
  name: string; // 这里不定义的话，constructor 里就不能初始化
  age: number; // = 0
  // TypeScript 中要么属性有默认值，要么在 constructor 中有赋值，不然就报错
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  sayHi(msg: string): void {
    console.log(`I am ${this.name}, ${msg}`);
  }
}
```

访问修饰符

```ts
class Person {
  public name: string; // 默认就是公有属性，不加 public 也是可以的
  private age: number = 0;
  protected gender = true;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  sayHi(msg: string): void {
    console.log(`I am ${this.name}, ${msg}`);
  }
}
const tom = new Person("tom", 18);
console.log(tom.age); // 报错，age 是私有属性
console.log(tom.gender); // 报错，gender 是收保护的属性
class Student extends Person {
  private constructor(name: string, age: number) {
    // 如果 constructor 是 private 的，就不能在外部实例化也不能继承了，只能在类的静态方法中去实例化
    // 如果 constructor 是 protected 的，只是不能再外部去实例化，但是可以继承
    super(name, age);
    console.log(this.gender); // 子类实例可以去访问父类的 protected 的属性
  }
  static create(name: string, age: number) {
    return new Student(name, age);
  }
}
const jack = Student.create("jack", 18);
```

readonly 只读，只能在属性定义默认值和 constructor 中赋值二选一，不能两者同时存在，一旦初始化了就不能再修改了

```ts
class Person {
  protected readonly gender: boolean;
  constructor(gender?: boolean) {
    this.gender = gender || true;
  }
}
class Person {
  protected readonly gender: boolean = true;
  constructor() {
    // this.gender = gender || true
  }
}
```

###类与接口

就好比手机和座机，手机和座机都可以打电话，但是座机功能比较少，不能发短信，没有 app，手机却都可以

不同的类也可能有一些相同点，这样我们就可以用接口去抽象公共特征

```ts
// 在 java 和 c# 这一类语言中，建议我们每一个接口的定义应该足够简单和细化
// 这里抽象的 eat 和 run 是两个能力，但这两个能力是不一定同时存在的
// interface EatAndRun {
//   eat (food: string): void
//   run (distance: number): void
// }
interface Eat {
  eat(food: string): void;
}
interface Run {
  run(distance: number): void;
}
class Person implements Eat, Run {
  eat(food: string): void {
    console.log(`优雅地进餐：${food}`);
  }
  // 这里不实现 run 方法的话就是会报错的
}
```

###抽象类

抽象类和接口有点类似，不同的是，抽象类可以包含具体的实现，而接口只抽象成员

```ts
abstract class Animal {
  // 抽象类不能实例化，只能通过子类去继承再实例化
  eat(food: string): void {
    console.log(`呼噜呼噜地吃：${food}`);
  }
  abstract run(distance: number): void; // 抽象方法也是不具体实现的，且子类一定要去实现
}
class Dog extends Animal {
  run(distance: number): void {
    console.log("爬行", distance);
  }
}
const d = new Dog();
d.eat("apple");
d.run(100);
```

###泛型

generics 就是指我们在定义函数、接口或类的时候，我们没有去指定具体的类型，等我们去使用的时候再去指定具体的类型的特征，这样是为了极大的复用我们的代码

```ts
function createNumberArray(length: number, value: number): number[] {
  const arr = Array<number>(length).fill(value);
  return arr;
}
function createStringArray(length: number, value: string): string[] {
  const arr = Array<string>(length).fill(value);
  return arr;
}
// 下面这一个函数就完成了上面两个函数中功能
function createArray<T>(length: number, value: T): T[] {
  const arr = Array<T>(length).fill(value);
  return arr;
}
const res = createArray<number>(3, 100);
const res = createArray<string>(3, "foo");
```

###类型声明

Type Declaration，实际开发中我们会使用第三方模块，第三方模块不一定是用 TypeScript 编写的，所以它的成员就没有强类型的体验

```ts
import { camelCase } from "lodash";
declare function camelCase(input: string): string; // 如果没有声明文件，可以自己 declare 去声明
const res = camelCase("hello world!");
```

社区比较强大，一些常用的第三方模块会有类型声明的包，安装这些包就可以了，这些包都是开发依赖，一般都是 `.d.ts` 结尾的文件，表示这些都是声明文件

