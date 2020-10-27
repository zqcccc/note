# ECMAScript

es6 也是 es2015，并且以后都以年份命名，有的人也会用 es6 来泛指 es6 及以后发布的新的内容，因为 es6 相比 es5.1 变化比较大，但 es6 就只是 es2015 而已，像 async/await 就不是 es2015 的标准，是 es2017 的内容

需要阅读原文的话
[ECMAScript® 2015 Language Specification](http://www.ecma-international.org/ecma-262/6.0/)

es2015 的新特性

- 解决原有语法上的一些问题或者不足
- 对原有语法进行增强
- 新的对象、新的方法、新的功能
- 全新的数据类型和数据结构

[查看 node 各版本支持新标准的情况](node.green)

## 作用域

js 原本没有块级作用域

```js
for (let i = 0; i < 3; i++) {
  let i = "foo"; // 这里是不会报错，for 循环语句有它本身的作用域
  console.log(i);
}

// 约等于下面
let i = 0; // 这里只是演示，其实 i 没有申明在外层的作用域
if (i < 3) {
  let i = "foo";
  console.log(i);
}
i++;
if (i < 3) {
  let i = "foo";
  console.log(i);
}
i++;
if (i < 3) {
  let i = "foo";
  console.log(i);
}
i++;
```

var 声明的变量有变量提升的官方 bug，不过也不能弃用这个关键字，弃用的话可能会导致以前的代码报错

const 就是在 let 基础上多了“只读”属性，变量一旦声明就不能修改，const 一定要有初始值

现在一般是不用 var，然后主要用 const，看需要再用 let

## Destructuring 

### 数组解构
很简单，就是原来变量名位置变成了方括号，然后自己根据需要去指定相应位置的变量名就行

```js
const arr = [1, 2, 3];
const [, , c] = arr; // c 就是 3

const [a, ...rest] = arr; // ...只能用在最后一个成员变量

const [, , , novar = "abc"] = arr; // 指定默认值
```

### 对象解构
对象解构是原来变量名位置多了花括号，对象是根据属性名去提取解构的
因为提取的变量名可能和现在的作用域里的变量名相同导致冲突，所以对象解构是可以重命名的

```js
const obj = { a: 1, b: 2 };
const a = 123;
const { a: objA } = obj; // 重命名以解决命名冲突
```

## 模板字符串

就是\`\`支持换行，嵌入变量或者任何 JavaScript 的代码

_带标签的模板字符串_

```js
const name = "Tom";
const gender = 1;
function myTagFunc(strings, name, gender) {
  // strings: ['hey, ', ' is a ', '.']
  const sex = gender === 1 ? "man" : "woman";
  return strings[0] + name + strings[1] + sex + strings[2];
}
const result = myTagFunc`hey, ${name} is a ${gender}.`;
// hey, Tom is a male.
```

标签模板字符串就是对模板字符串进行更复杂的加工

## 字符串

新增方法

- `includes()`
- `startsWith()`
- `endsWith()`

用来查找字符串，都返回布尔值

## 参数默认值

形参默认值 default parameters

```js
function opt(a, b = "123") {
  // 参数默认值只能在后面
}
```

## 剩余参数

```js
function opt(a, ...rest) {
  // 参数默认值只能在后面
}
```

取代 `arguments`，只能接在形参的最后一位，只能用一次

## 展开数组

```js
const arr = [1, 2, 3, 1];
console.log.apply(console, arr);
// 等价于
console.log(...arr);
```

## 箭头函数

arrow function 会让代码更简短

arrow 不会改变 this 的指向，或者说它没有自己的 this

```js
const obj = {
  name: "tom",
  sayHi: function () {
    console.log(`hi, ${this.name}`);
  },
  sayHiArrowFunc: () => {
    console.log(`hi, ${this.name}`);
  },
  sayHiAsync: function () {
    setTimeout(() => {
      console.log(`hi, ${this.name}`);
    }, 100);
  }
};
obj.sayHi(); // hi, tom
obj.sayHiArrowFunc(); // hi, undefined
obj.sayHiAsync(); // hi, tom
```

没有自己的 this，不能 new，没有 arguments，不能用 yield

## 对象字面量增强

```js
const a = "a";
const obj = {
  a, // key 和 value 相同就可以这样写
  say() {
    // 这里其实就是 function 的简写
    console.log(a);
  },
  [Math.random()]: 123 // 计算属性名
};
// 以上三点都是 es6 才有的
```

## 对象拓展方法

- `Object.assign` 将多个源对象中的属性复制到一个目标对象中（浅拷贝）
- `Object.is` 可以区分 `+0` 和 `-0`，`NaN` 也等于 `NaN`（其实用的不多，业务一般不涉及这些）

## Proxy

代理对象

```js
const person = {
  name: "zce",
  age: 20
};

const personProxy = new Proxy(person, {
  get(target, property) {
    return property in target ? target[property] : "default";
  },
  set(taget, property, value) {
    if (property === "age") {
      // 做数据校验
      if (!Number.isInteger(value)) {
        throw new TypeError(`${value} is not an int`);
      }
    }
    target[property] = value;
  }
});
personProxy.age = "123";
```

_对比 `Object.defineProperty`_

`defineProperty` 只能监视到属性的读写
`Proxy` 能够监视到更多的对象操作

| handle 方法              | 触发方式                                                     | Proxy 独有 |
| ------------------------ | ------------------------------------------------------------ | ---------- |
| get                      | 读取某个属性                                                 | ×          |
| set                      | 写入某个属性                                                 | ×          |
| has                      | in 操作符                                                    | ✔️         |
| deleteProperty           | delete 操作符                                                | ✔️         |
| getPrototypeOf           | Object.getPrototypeOf()                                      | ✔️         |
| setPrototypeOf           | Object.setPrototypeOf()                                      | ✔️         |
| isExtensible             | Object.isExtensible()                                        | ✔️         |
| preventExtensions        | Object.preventExtensions()                                   | ✔️         |
| getOwnPropertyDescriptor | Object.getOwnPropertyDescriptor()                            | ✔️         |
| defineProperty           | Object.defineProperty()                                      | ✔️         |
| ownKeys                  | Object.getOwnPropertyNames()、Object.getOwnPropertySymbols() | ✔️         |
| apply                    | 调用一个函数                                                 | ✔️         |
| construct                | 用 new 调用一个函数                                          | ✔️         |

```js
const person = {
  name: "zce",
  age: 20
};

const personProxy = new Proxy(person, {
  deleteProperty(target, property) {
    console.log("delete", property);
    delete target[property];
  }
});
delete personProxy.age;
```

Proxy 更好的支持数组的数组对象的监视
以前都是重写数组的操作方法

```js
const list = [];
const listProxy = new Proxy(list, {
  set(target, property, value) {
    console.log("set", property, value); // 这里就是监听数组的操作
    target[property] = value;
    return true; // 表示操作成功
  }
});
```

Proxy 是以非侵入的方式监管了对象读写

## Reflect

统一对象操作 API

Reflect 属于静态类，不能 ~~new Reflect()~~，Reflect 内部封装了一系列对对象的底层操作，Reflect 成员方法就是 Proxy 处理对象的默认实现

就是说在 new 一个 `Proxy` 的时候，没有添加的处理方法，都用 Reflect 上相对应的静态方法去处理了

```js
const person = {
  name: "zce",
  age: 20
};

const personProxy = new Proxy(person, {
  get(target, property) {
    console.log("watch logic");
    return Reflect.get(target, property);
  }
});
```

那为什么要有这个对象呢？

`Reflect` 对象的意义可能是，统一提供了一套用于操作对象的 API
以前的 `in`，`delete`，`Object.keys` 一下是操作符的，一下又是方法的，比较混乱，`Reflect` 把这些操作都统一到了一起

## Promise

一种较优的异步解决方案，链式调用，解决了传统异步编程中回调函数嵌套过深的问题

## Class 类

以前都是用 function 和 prototype 去实现的类，现在 `class` 更像是一个语法糖

### static 静态成员

实例方法是新建实例后才能调用
静态方法是用类去调用

### extends 类的继承

更直观，更方便

## Set

值不重复的集合

for of 遍历

```js
// 去重
const arr = [1, 2, 3, 4, 1, 3, 5];
const res = Array.from(new Set(arr));
const res = [...new Set(arr)];
```

## Map

让 key 可以不光只是 string，Map 才是真正意义上的键值对集合

## Symbol

一种全新的原始数据类型，用来表示一个独一无二的值

`typeof` 判断也是 `symbol`

es2015 之后对象的 key 不光能是 string，还能是 symbol

可以用来创建私有成员

现在最主要的作用就是为对象添加独一无二的属性名

```js
// Symbol.for() 只维护 symbol 和 字符串之间的关系
Symbol.for(true) === Symbol.for("true"); // true

const obj = {
  [Symbol.toStringTag]: "xxx"
};
console.log(obj.toString()); // [object xxx]
```

用 Symbol 作为 key 的对象，用 `for in` 或者 `Object.keys()` 或者 `JSON.stringify` 都是拿不到这个 symbol key 的

用 `Object.getOwnPropertySymbols()` 可以获取对象所有的 Symbol key

## for of 循环

for 循环适合遍历数组

for in 循环适合遍历键值对

for of 作为遍历所有数据结构的统一方式（数据有实现 Iterable 接口）

arr.forEach() 不能跳出循环

arr.some() 或者 arr.every() 才能跳出循环

for of 可以直接跳出循环

for of 不能直接遍历 Object

## Iterable 可迭代接口

ES 中能够表示有结构的数据类型越来越多，ES2015 提供了 Iterable 接口，实现 Iterable 接口就是 for...of 的前提

**对象要实现 Iterable 可迭代接口的方法，Iterable 要有 \[Symbol.iterator\] 的方法，这个方法返回一个 Iterator 对象，Iterator 要有用于迭代的 next 方法，next 方法执行返回的是迭代结果 IterationResult 对象，这个对象要有 value 和 done 属性来表示当前被迭代的数据，value 可以是任意类型，done 要是布尔类型**

```js
const obj = {
  store: ["foo", "bar", "baz"],

  [Symbol.iterator]: function () {
    let index = 0;
    const self = this;

    return {
      next: function () {
        const res = {
          value: self.store[index],
          done: index >= self.store.length
        };
        index++;
        return res;
      }
    };
  }
};

for (const item of obj) {
  console.log("循环", item);
}
// 循环 foo
// 循环 bar
// 循环 baz
```

## Iterator 迭代器模式

对象实现对外统一的数据遍历接口，调用者就不用关心内部的数据结构如何去遍历，只需要关心自己的逻辑就行了

ES2015 是在语言层面实现了迭代器模式，可以适用于任何数据结构，只要实现这个迭代器的逻辑就行了

## Generator 生成器

避免异步编程中回调嵌套过深的问题

在 function 后面加一个 `*` 就是一个 generator 函数了

生成器对象也实现了 iterator 接口

generator 函数要配合 `yield` 关键字去使用

generator 对象是惰性执行的，调用一次才会继续向下执行到 `yield` 的语句，直到执行完

### 应用

```js
// 发号器
function* createIdMaker() {
  let id = 1;
  while (true) {
    yield id++;
  }
}
const idMaker = createIdMaker();
idMaker.next();
idMaker.next();
idMaker.next();
// 可以一直往下执行，程序也不会卡死
```

```js
// 使用 Generator 函数实现 Iterator 方法
const obj = {
  life: ["foo", "bar", "baz"],
  learn: ["foo", "bar", "baz"],
  work: ["foo", "bar", "baz"],

  [Symbol.iterator]: function* () {
    const all = [...this.life, ...this.learn, ...this.work];
    for (const item of all) {
      yield item;
    }
  }
};

for (const item of obj) {
  console.log("循环", item);
}
```

## ES Modules

语言层面的模块化标准

## ECMAScript 2016

新增 `Array.prototype.includes` 方法，原来的 indexOf 方法也能查找，不过不能查找到 NaN

新增指数运算符，以前用的是 `Math.pow(2, 10)` 来求的 2 的 10 次方，指数运算符就是写成 `2 ** 10`

## ECMAScript 2017

- Object.values
- Object.entries
- Object.getOwnPropertyDescriptors
- String.prototype.padStart / String.prototype.padEnd
- 在函数参数中添加尾逗号
- Async / Await，Promise 的语法糖，彻底解决函数嵌套
