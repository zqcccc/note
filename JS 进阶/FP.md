# 函数式编程

是一种编程范式

[函数式编程的历史](https://zhuanlan.zhihu.com/p/24648375?refer=marisa)

纯函数指的是数学意义上的映射关系

纯函数好处

- 可测试
  - 纯函数让测试更方便
- 并行处理
  - 在多线程环境下并行操作共享的内存数据很可能会出现意外情
  - 纯函数不需要访问共享的内存数据，所以在并行环境下可以任意运行纯函数 (Web Worker)

## 柯里化 (Currying)

- 当一个函数有多个参数的时候先传递一部分参数调用它（这部分参数以后永远不变）
- 然后返回一个新的函数接收剩余的参数，返回结果

```js
// 柯里函数
function curry (func) {
  return curriedFn = (...args) => {
    // 判断实参和形参的个数
    return args.length < func.length 
    ? (...moreArgs) => curriedFn(...args.concat(moreArgs))
    : func(...args)
  }
}
```

## 函数组合（compose）

```js
const compose = (...args) => value => args.reverse().reduce((acc, fn) => fn(acc), value)
```

函数组合要满足结合律 (associativity)：

```js
// 结合律（associativity）
let f = compose(f, g, h)
let associative = compose(compose(f, g), h) == compose(f, compose(g, h)) // true
```



## Point Free

把数据处理的过程定义成与数据无关的合成运算，不需要用到代表数据的那个参数，只要把简单的运算步骤合成到一起，在使用这种模式之前我们需要定义一些辅助的基本运算函数。 

- 不需要指明处理的数据
- 只需要合成运算过程
- 需要定义一些辅助的基本运算函数

简单的说就是 compose

## Functor (函子)

- 函数式编程的运算不直接操作值，而是由函子完成 函子就是一个实现了 map 契约的对象
- 我们可以把函子想象成一个盒子，这个盒子里封装了一个值
- 想要处理盒子中的值，我们需要给盒子的 map 方法传递一个处理值的函数（纯函数），由这个函数来对值进行处理 
- 最终 map 方法返回一个包含新值的盒子（函子）

静态 `of` 方法来返回一个新的自己，以便链式调用

`map` 方法接受一个纯函数，对自己的值进行操作

### MayBe函子

作用就是处理外部的空值

在执行 `map` 时会对函子的值进行检查，如果是控制就不执行传入 `map` 的函数

### Either函子

就是组合两个函子，一个就是正常的函子，另一个函子是 `map` 方法只会返回自己

这样在写一个函数的时候，用 `try catch` 去写，正常就执行前者，出错了就执行后一个函子

### IO函子

带副作用的操作终究是不能避免的，但是我们可以延迟这个不纯的操作

从这个函子开始就把函子的 `value` 当成了一个函数

这样每次在 `map` 的时候，都会进行 compose

```js
map(fn) {
	return new IO(compose(fn, this._value))
}
```

最后在调用的时候才有副作用

### folktble

一个标准的函数式编程库，没有多的功能函数

### Task函子

是 folktale 中的一个函子，接受像 Promise 构造器参数一样的函数

`map` 接受纯函数，`run` 执行，`listen` 方法接受一个对象，上面有 `onRejected` 和 `onResolved` 两个属性，都是（事件）函数，以这种事件监听的方式去查看结果

### Pointed函子

实现了 of 静态方法的函子

of 方法是为了避免使用 new 来创建对象，更深层的含义是 of 方法用来把值放到上下文 Context（把值放到容器中，使用 map 来处理值）

前面都有用到这个概念

### Monad 单子

IO 函子嵌套不好用，所以有了 Monad

有 join 和静态 of 两个方法

`join` 直接返回函子中值的调用（函数调用）

`flatMap` 就是先 map 后得到有最新的一大堆操作的函数的函子，再调用 join 去执行（等于解开一个函子的嵌套，并马上执行了函数），得到的值又给传到了后面，如果后面的函数又返回了一个函子，这样就是有一个带闭包的函数函子（）

简单说就是函数直接返回值或者函数的就用 map，直接返回函子的用 flatMap

