# 函数中的 this

JavaScript 函数中的 this 与其他语言相比，会有些不同，同时在严格模式下和非严格模式下也会有一些差别。随着前端生态的完善以及为了避免同学们对 this 造成误解和困扰，以下内容都默认是基于严格模式来讲解。

首先我们记住一个概念：**在函数内部，this 的值取决于函数被调用的方式。**

在严格模式下，this 将保持他进入执行上下文时的值，所以下面的 this 是 undefined。

```js
function func1() {
  "use strict";
  return this;
}
console.log("func1", func1()); // func1 undefined
```

## 通过 call 或 apply 来传递 this

我们可以通过 call 和 apply 来将 this 值绑定到特定对象，call 和 apply 是继承自 Function.prototype。

```js
let userA = {
  name: "Bill",
  age: 16
};
let userB = {
  name: "Richard",
  age: 18
};

function sayHello() {
  return this.name;
}

sayHello.call(userA); // Bill
sayHello.call(userB); // Richard
```

## call 和 apply 中的参数传递

call 方法接受若干个参数，其中第一个是供 this 绑定使用，后续参数会依次传递给调用的函数。
apply 方法接受两个参数，第一个同样是供 this 绑定使用，第二个参数是 array 类型，可以将函数所需的参数依次放入该数组传递给函数。

```js
let userA = {
  name: "Bill",
  age: 16
};
let userB = {
  name: "Richard",
  age: 18
};

function saySomething(word) {
  return this.name + "," + word;
}

saySomething.call(userA, "let's go!"); // Bill
saySomething.call(userB, ["Nice to meet you!"]); // Richard
```

## bind 方法

> ECMAScript 5 引入了 Function.prototype.bind。调用 f.bind ( someObject )会创建一个与 f 具有相同函数体和作用域的函数，但是在这个新函数中，this 将永久地被绑定到了 bind 的第一个参数，无论这个函数是如何被调用的。

这里要注意的一点是 bind 方法只会生效一次。

```js
function func1() {
  return this.name;
}

let userA = func1.bind({ name: "Richard" });
console.log(userA()); // Richard

let userB = userA.bind({ name: "Bill" });
console.log(userB()); // Richard
```

## 箭头函数

箭头函数是 ECMAScript 6 中新加入的「语法糖」。

箭头函数中的 this 与函数所在的上下文 this 保持一致，即箭头函数体内的 this 对象，就是其被定义时所在的对象，而不是使用时所在的对象。

看到上一句话是不是会觉得和之前提醒的 **在函数内部，this 的值取决于函数被调用的方式。** 自相矛盾？

之所以说箭头函数是语法糖，也正是由于上述原因，实际上箭头函数是没有 this 的。我们来看以下代码：

```js
// from:  http://es6.ruanyifeng.com/#docs/function#%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0

// ES6
function foo() {
  setTimeout(() => {
    console.log("id:", this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function() {
    console.log("id:", _this.id);
  }, 100);
}
```

在上述代码中，setTimeout 中的函数实际并没有 this，而是直接从声明它的地方取值。所以，箭头函数中的 this 不是真正意义上的 this。

正因为箭头函数没有真正的 this，所以箭头函数也不能被当做构造函数。