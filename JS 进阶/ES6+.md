# ES6~现在

## 模块化

模块作用域

自动严格模式

```html
<script type="module"></script>
```

导入和导出都是可以重命名的

```js
// moduleA.js
export {
	a as b
	others
}

import { b as c } from './moduleA.js'
// 或者直接全部导入
import * as m from './moduleA.js'
// m 上就有 b 和 others 和 default 属性

import('./moduleA.js').then(function (m) {
  // 这样是异步导入
})
```

## 定义变量

let 和 const 有死区的概念，都不能重复定义，有了他们的存在 js 才有了块级作用域

## 字符串

模板字符串

## 函数

[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

```js
const test = {
  a: 1,
  es5: function() {
    return this.a
  },
  es6: () => this.a
}
test.es5() // 1
test.es6() // undefined
```

箭头函数虽然没有 arguments 但是有拓展参数

## 数组

静态方法 `of` 和 `from`

`Array.from` 可以接收一个迭代器

`Array.from()` 和 `new Array()` 不一样的地方是，如果 `new Array` 只接受一个数字参数的话，会返回数字长度的空数组，传多个参数时又相当于 `[ ]`，`Array.from()` 保证传入多少个参数行为都一样

`.find()` 用于找出第一个符合判断条件的元素

`.findIndex()` 用于找出第一个符合判断条件的元素下标

`.includes()` 用于判断是否存在某个值，和 `indexOf` 不一样的地方是可以判断 `NaN`

`.fill()` 用一个元素填充整个数组

`.flat()` 用于辗平多维数组

`.flatMap()` 等于 `.map().flat()`

## 对象

`Object.is` 可以判断 `NaN` 还有 0 和 -0 不相等

判断 -0 可以自己写个方法

```js
function isNegativeZero(n) {
	return 0 === n && 1 / n === -Infinity
}
```

## class

class 和 const、let 一样都不会变量提升，都有死区

本质上还是 function

不管外部有没有 `"use strict"` class 内部都强制使用严格模式

必须使用 new 关键字

### 继承

super 在 constructor 中指的是父类的构造器，在普通的方法中是父类

## 新数据结构

Map

Set

### Symbol

互不相同的“字符串”，可作为属性名

实现类的私有属性

## 异步编程

`then()` 的参数一般是一个函数，函数的参数是上一个 Promise 返回的结果，这个 then 默认返回一个新的 Promise，如果 then 里的函数没有返回值其实就是隐式返回了 `undefined`

如果 `then()` 里不是一个函数，是一个普通的值，那就无视掉