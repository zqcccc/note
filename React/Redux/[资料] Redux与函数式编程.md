---
enable html: true
---

# Redux与函数式编程

## 什么是函数式编程？

函数式编程（Functional programming）是指一种编程典范，它避免使用程序状态和易变对象，倡导利用若干简单的执行单元让计算结果不断渐进，逐层推导复杂的运算，而不是设计一个复杂的执行过程。

## 函数式编程的特点

### 函数是第一等公民

在 JavaScript 中，函数本就是第一等公民。JavaScript 中的函数可以作为参数传递，可以赋值给变量，可以作为函数返回值，还可以把它们存储到数组中，它与其他数据类型一样，处于平等地位。

### 只使用表达式，不使用语句

函数式编程的每一步都是一个有返回值的表达式（expression），而不是多个语句（statement）。它强调将复杂的计算过程拆分成多个可复用的函数。

### 没有副作用的纯函数

函数式编程要求我们编写的每个函数都是独立而无副作用的，不会修改系统中的状态值，且只会返回一个新的值。同时的，函数在相同的输入值时，需产生相同的输出。

## 函数式编程的基本运算

### 柯里化（curry）

函数的柯里化，即对于一个多参数的函数进行一次转化，转化后的函数只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

### 函数的合成（compose）

数据需要经过多个函数才可以变成另一个值，把这多个函数像一系列管道拼接起来，这个过程就叫函数合成（compose）。

这就是函数组合：

```js
var compose = function(f,g) {
  return function(x) {
    return f(g(x));
  };
};

```

例如，我们要是实现一个函数，它接收一个字符串，然后需要将该字符串转换成大写，并在尾部添加一个感叹号。我们可以把这两个职责分派给 toUpperCase 和 exclaim 函数：

```js
// 把输入的字符转成大写
var toUpperCase = function(x) { return x.toUpperCase(); };
// 在所输入的字符末尾添加感叹号
var exclaim = function(x) { return x + '!'; };

```

这个时候我们还需要一个函数将这两个函数组合起来，以下有两种写法。

函数组合的写法：

```js
var shout = compose(exclaim, toUpperCase);

shout("send in the clowns");

```

函数嵌套的写法：

```js
var shout = function(x){
  return exclaim(toUpperCase(x));
};

shout("send in the clowns");

```

相比在函数中嵌套一大推函数的调用，借用 compose 方法，我们可以写出更为灵活易读的函数组合。

## Redux 中函数式编程的应用：中间件

### 中间件的组合：applyMiddleware 函数解析

我们先来回顾 applyMiddleware 函数的用法：

```js
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk';
import createLogger from 'redux-logger';

logger = createLogger();
const store = createStore(
  Reduces,
  applyMiddleware(thunk, logger),
)

```

如上，applyMiddleware 函数接收多个中间件函数，并将其串联起来。这种引入中间件的方式就很适合使用函数的组合（compose），事实上该函数也是这么实现的。

applyMiddleware 源码：

```js
export default function applyMiddleware() {
  for (var _len = arguments.length, middlewares = Array(_len), _key = 0; _key < _len; _key++) {
    middlewares[_k ey] = arguments[_key];
  }

  return function (createStore) {
    return function (reducer, preloadedState, enhancer) {
      var store = createStore(reducer, preloadedState, enhancer);
      var _dispatch = store.dispatch;
      var chain = [];

      var middlewareAPI = {
        getState: store.getState,
        dispatch: function dispatch(action) {
          return _dispatch(action);
        }
      };
      chain = middlewares.map(function (middleware) {
        // 为中间件函数传递 Store 的 Api, 返回一个新的中间件函数
        return middleware(middlewareAPI);
      });
      _dispatch = compose.apply(undefined, chain)(store.dispatch);

      return _extends({}, store, {
        dispatch: _dispatch
      });
    };
  };
}

```

如上，applyMiddleware 函数通过组合函数 compose，对新生成的中间函数实现链式调用。相比嵌套函数调用的写法，这种写法十分直观灵活，可维护性也极佳。
除了compose 的使用，还可以看到，我们传入的中间件函数并不会只执行一次： middlewares 作为原始中间件的存储数组，其包含的中间件函数会被遍历执行，接收一个中间件 Api middlewareAPI， 并返回一个新的中间件函数。事实上中间件函数在此处执行了部分运算，得到一个更为强大的新函数，让新函数去执行后面的操作。这正是一种柯里化的写法，不需要一次性传递所有的参数，而是将多个参数分多次传入。事实上中间件函数总共会被执行三次之多，下文我们会对其每一次的执行展开详解。

#### Redux 中的 compose

compose 合并中间件函数的方式十分简单灵活，而它的源码实现也十分简洁：

```js
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}

```

compose 巧妙的利用 reduce 函数对中间件函数进行累加，这种方式一开始看起来可能会比较绕，我们可以看下 compose 所生成的函数是什么样的：

```js
// 例如，我们向 compose 函数传入3个函数 aFunc，bFunc，cFunc
let reduceFuncA = compose.apply(aFunc, bFunc, cFunc);
// reduceFuncB 和 reduceFuncA 是等价的
let reduceFuncB = function(args) {
  aFunc(bFunc(cFunc(args)));
};
// 执行1
reduceFuncA(args);
// 执行2，和执行1等价
aFunc(bFunc(cFunc(args)));

```

中间件函数的组合也正是如此，例如当我们引入 thunk 和 logger 这两个中间件时，compose 方式组合的函数如下：

```js
_dispatch = compose.apply(undefined, [newThunk, newLogger])(store.dispatch);

```

非 compose 组合的函数：

```js
_dispatch = (function(args) {
  newThunk(newLogger(args))
})(dispatch);

```

当中间件的数量不断增多的情况下，中间件嵌套调用的方式可读性十分糟糕。compose 组合的方式赋予了中间件更大的灵活性：可以像管道一样随意且无限制的组合，而且不用担心代码的可读性。

### 中间件的实现

我们先来实现一个简单的日志中间件 actionLogger，用于记录我们分发的 action 类型：

```js
const actionLogger = (store) => {
    return (next) => (action) => {
        console.log('打印 action 类型', action.type);
        next(action); 
    }
};

```

它的整个调用流程可以写成：

```js
actionLogger(store)(next)(action);

```

可以看到，中间件函数把参数分为三次传入调用，很典型的柯里化写法。我们来分析一下这个中间件函数 actionLogger 每一次的部分运算是如何执行的。

#### 第一次调用：actionLogger(store)

actionLogger 第一次执行所接收的 store 参数其实就是 applyMiddleware 函数为中间件函数封装的一个简洁版的 Store，包含了 Store 中的 getState 方法和 一个被中间件加强的 dispatch 方法，我们在上面介绍 applyMiddleware 函数的时候也有提到，代码如下：

```js
chain = middlewares.map(function (middleware) {
  // 为中间件函数传递 Store 的 Api, 返回一个新的中间件函数
  return middleware(middlewareAPI);
});

```

第一次执行后返回的新函数形态如下：

```js
(next) => (action) => {
    console.log('打印 action 类型', action.type);
    next(action);
}

```

通过第一次的部分运算，我们可以得到一个能够访问中间件 Api 的的新函数，而这可以帮助我们实现更强大的中间件。

#### 第二次调用：actionLogger(store)(next)

我们上面有提到，_dispatch 在不使用 compose 组合的时候如下：

```js
_dispatch = (function(args) {
  otherLogger(actionLogger(args))
})(dispatch);

```

可以看到，最末端的中间件 actionLogger 接收的正是 Store 原始的 dispatch 方法。

我们再来看中间件第二次执行后返回的函数形态：

```js
(action) => {
  console.log('打印 action 类型', action.type);
  next(action);
}

```

很显然，中间件此时返回的正是一个类 dispatch 函数，而它也将作为下一级的中间件函数的 next 参数。简单来说，只有最末端的中间件函数的 next 参数是不经过加工的 store.dispatch，其余中间件的 next 参数都是其后一个中间件函数加强过的 dispatch 方法。这是一种链式的关系，dispatch 的控制权在中间件中由内而外进行传递，对于 compose 组合的顺序来说则是从右至左。

#### 第三次调用：actionLogger(store)(next)(action)

最后一次的调用正是用于 action 的分发，和第二次从右至左传递 next 参数顺序相反，此时的调用顺序是从左往右。此时会先执行排最前面的中间件函数，通过对 next 参数的调用才一步步执行到最末端的中间件函数的 next 方法，也就是 store.dispatch。

### Redux 中更多的函数式编程

Redux 中间件系统十分灵活强大，得益于代码中函数式编程的思想。不仅仅是中间件，Redux 中的Reducer 也是一个很典型的函数式编程，它要求我们保证函数的‘纯’，不允许 Reducer 函数带有任何的副作用，而这种函数式的编程模式也可以帮助我们杜绝多数意想不到的问题。

关于 Redux 中的函数式编程，建议大家通过阅读它的源码来细细体会。