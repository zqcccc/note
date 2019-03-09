# 立即调用的函数表达式（IIFE）

大家在学习 JavaScript 的过程中，可能会看到许多下面的或者类似的代码：

```js
(function(){
  //do something here;
})();
```

对于上面的代码，有两种说法，一部分人称之为 **自执行的匿名函数（self-executing anonymous function）**，另外一部分则更为推崇 [Ben Alman](http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife) 的叫法：**立即调用的函数表达式（IIFE，Immediately-Invoked Function Expression ）**。事实上，大家可以按照自己的理解来选择符合的说法。

## 立即调用函数会报错

在Javascript中，一对圆括号`()`是一种运算符，我们可以使用圆括号来调用一个函数，如调用 `sayHello` 方法则可以这样做。

```js
var sayHello = function() {
  // some code
}
// 使用圆括号表达式调用
sayHello();
```

有时候，我们可能会和上面的代码一样，在定义一个函数后，立即且只调用一次该函数。然而当我们按照下面这样写的时候，发现会报错。

```js
// 定义了一个匿名函数
function () {
  // some code
}();
// SyntaxError: Unexpected token (
```

之所以产生这个错误的原因是，JavaScript引擎在解析发现 `function` 关键字出现在行首，认为这一段代码是函数申明语句，因此在 `function 关键字后面需要的是一个函数标识符名称，而对于标识符来说是不能以 `(` 来命名的。

因此，我们给该函数加上命名。但是发现还是会报一个不一样的错误：

```js
// 定义了一个匿名函数
function sayHello() {
  // some code
}();
// SyntaxError: Unexpected token )
```

原因其实前面也提及到了，JavaScript引擎在解析发现 `function` 关键字出现在行首，认为这一段代码是函数申明语句。而函数申明语句并不能通过圆括号 `()` 来结尾，因此就报错了。

## 立即调用的函数表达式

上面的问题的解决方法就是，让我们**函数申明语句**转换成**函数表达式**，即使 `function` 关键字不出现在行首，让 JavaScript 解析引擎将其理解为一个表达式，避免错误。要使其成为一个表达式，有很多种方法。其中最简单且最容易阅读的方式，便是使用圆括号来实现。

```js
(function(){ /* some code */ }());
// 或者可以这样写
(function(){ /* some code */ })();
```

因此我们把上面的表达式叫做 **立即调用的函数表达式(Immediately-Invoked Function Expression)** 也就是 **IIFE**。

## 其他的写法

除了上面的写法外，还有许多能够让 JavaScript 解析器解析成表达式的写法，如下面的写法：

```js
true && function(){ /* some code */ }();

// 或者使用操作符
!function(){ /* some code */ }();
~function(){ /* some code */ }();
-function(){ /* some code */ }();
+function(){ /* some code */ }();
```

## IIFE 的好处

大多数情况，我们使用到 **IIFE** ，并不会被再次调用，因此我们通常会使用**匿名函数**，即忽略立即执行的函数的函数名称。

我们可以使用 **IIFE** 来形成了一个私有的作用域（模拟块级作用域），因此我们可以封装想要的私有变量，以及避免变量出现冲突的情况。

```js
// 可被访问 canRead
var canRead = 2;
var same = 1;

// 私有变量 noRead
(function (){
  var noRead = 3;
  var same = 2;
})();

console.log(canRead); // 2
console.log(noRead); // noRead is not defined
// IIFE 定义变量不会污染外部的变量
console.log(same); // 1
```

## IIFE 创建 Module

我们可以利用 **IIFE** 来创建私有的函数作用域，不仅可以避免变量冲突还可以创建私有变量和方法，十分适合用来创建一个模块，如下所示：

```js
// 创建一个立即调用的匿名函数表达式
// return一个变量，其中这个变量里包含你要暴露的东西
// 返回的这个变量将赋值给 module
var module = (function() {
  var num = 1;
  return {
    get: function () {
      return num;
    },
    set: function (value) {
      num = value;
    }
  }
})();
```

## 更多阅读

- [IIFE by Ben Alman](http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife)
- [立即调用的函数表达式（IIFE）- 阮一峰](http://javascript.ruanyifeng.com/grammar/function.html#toc24)