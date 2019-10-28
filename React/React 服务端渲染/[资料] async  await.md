# async 和 await

## 什么是 async 函数？

async 函数在 [ECMAScript 2017 (ECMA-262)](https://www.ecma-international.org/ecma-262/8.0/#sec-async-function-definitions) 中被标准定义，简单来讲，其是一个可自动执行的异步函数，也是 Generator 函数的语法糖。

> 注：有关 Generator 函数，本文不做过多介绍，推荐可以在[这里](http://es6.ruanyifeng.com/#docs/generator-async)了解更多

## async 函数的使用

### 基本使用

在 async 函数之前，为了解决 JavaScript 中的回调地狱问题，与 Promise 有关的标准被提出，Promise 可以将回调写法转化成链式写法，流程更加清晰，代码更加优雅。

这里我们有一个例子：

```
let getData = (a) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(a + 1)
        }, 2000)
    })
}

let getNextData  = (b) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(b + 1)
        }, 2000)
    })
}

const Fn = () =>{
    getData(1).then((res1) => {
        return getNextData(res1)
    }).then((res2) => {
        console.log(res2)
    })
}
Fn()
```

在以上的例子中，`getData` 和 `getNextData` 为我们模拟的两个数据请求函数，我们之后定义了 Fn 函数，其作用是串联地执行两个异步函数并且将最终结果打印。

使用 Promise 的链式写法已经比较直观了，但是如果我们采用 async 函数，可以如下这样写：

```
async function Fn2(){
    let res1 = await getData(1);
    let res2 = await getData(res1);
    console.log(res2)
}
Fn2()
```

### 获取返回结果

另外，我们值得注意的是 async 函数实际上是返回一个 Promise，也就是说，假设我们把上述函数改写成这样：

```
async function Fn2(){
    let res1 = await getData(1);
    return await getData(res1);
}
```

这个时候如果我们使用 `console.log(Fn2())` 同步打印，我们只可以打印出 Promise 对象本身，这个时候我们可以采取 `Fn2().then( res => console.log(res) )` 这种方式来获取最终结果。

### 错误处理

对于 async 函数来说，由于 await 后面跟着的 Promise 可能最终 reject 或者由于某些原因报错，这里我们以上面的代码段举例，对其中的 `getNextData` 和 `Fn2` 函数做一些修改：

```
let getNextData  = (b) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            reject(b + 1)
        }, 200)
    })
}

async function Fn2(){
    try {
        let res1 = await getData(1);
        return await getNextData(res1);
    } catch (e) {
        console.log('Error:', e)
    }
}
```

这个时候我们再运行 F2，就可以发现错误已经成功被捕获到被打印出来。

## async 函数的原理

我们之前有提到，async 函数实际上是 Generator 的语法糖，而 Generator 函数的不方便之处在于，其执行必须要依赖执行器，而 async 函数可以说是一个自带执行器的 Generator 函数。所以，对于上述的 async 函数：

```
async function Fn2(){
    let res1 = await getData(1);
    let res2 = await getData(res1);
    console.log(res2)
}
```

我们其实可以使用 Generator 函数写出其“等价”函数：

```
function fn3(){
    return run(function*() {
        let res1 = yield getData(1);
        let res2 = yield getData(res1);
        console.log(res2)
    });
}
```

其中这里，使用到了一个执行器函数 `run`, 这里我们对 `run` 函数做一个简单实现：

```
function run(genF) {
    return new Promise(function(resolve, reject) {
        var gen = genF();
        function step(nextF) {
            try {
                var next = nextF();
            } catch(e) {
                return reject(e);
            }
            if(next.done) {
                return resolve(next.value);
            }
            Promise.resolve(next.value).then(function(v) {
                step(function() { return gen.next(v); });
            }, function(e) {
                step(function() { return gen.throw(e); });
            });
        }
        step(function() { return gen.next(undefined); });
    });
}
```

本文讲解的有关 async 函数的内容，就到这里。