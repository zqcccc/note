# koa 简介

## koa 基本介绍

Node 的出现，可以让我们使用 JavaScript 编写服务端 web 应用，而使用服务端 web 框架，可以大大提高我们编写应用程序的效率和规范性，这里面比较著名的，应当属 express 和 koa。

express 是一个出现较早的基于 Node 的 web 开发框架，自身也封装了路由、视图处理等能力，使用起来比较方便，相比之下， koa 是 express 原班人马基于 ECMAScript 6 的新特性开发的框架，框架本身并不包含任何中间件，因此大部分功能都要借助第三方中间件来解决，但是由于其基于 generator 等特性的异步流程控制，解决了回调地狱和麻烦的错误处理等问题，因此广受开发者欢迎。

### koa 洋葱模型

我们上文提到，koa 主要工作都交给中间件来实现，实际上，koa 组织中间件的方式也非常经典，是采用一种洋葱模式来实现，下图是一张典型的 koa 中间件说明图：

![img](http://p.qpic.cn/qqconadmin/0/e26cb228b6c3466f880179d04feeb2e1/0)

为了更简便直观，我们也可以看这张图：

![img](http://p.qpic.cn/qqconadmin/0/9ef5bac239884c90a9522acf0caf5122/0)

当一个请求过来的时候，会依次经过各个中间件进行处理，中间件跳转的信号是 `await next();`，当程序运行到 `await next();` 的时候就会暂停当前程序，进入下一个中间件，处理完之后才会再回过头来继续处理。

一个样例代码如下：

```
const Koa = require('koa');

const app = new Koa();
const PORT = 3000;

// #1
app.use(async (ctx, next)=>{
    console.log(1)
    await next();
    console.log(1)
});
// #2
app.use(async (ctx, next) => {
    console.log(2)
    await next();
    console.log(2)
})

app.use(async (ctx, next) => {
    console.log(3)
})

app.listen(PORT);
console.log(`http://localhost:${PORT}`);
```

关于这段样例代码的运行环境以及运行方式，我们可以看完后半部分后，再来运行。另外关于 koa 具体的请求与响应逻辑处理，我们会在下半部分“koa上手使用”中介绍。

## koa 上手使用

### 基本使用

koa 要求使用 Node V7.6 以上的版本，但是本扩展编写之时，Node 已经更新到了 v10.8.0 版本，我们这里建议使用新版本的 Node，从而可以使用 Node 的新特性。

我们可以新建一个项目文件夹，在项目中，使用 npm 按照一般的包安装方式安装 koa:

```
npm i koa --save
```

我们可以使用 koa 搭建一个最简单的 web 服务端，我们新建 `server.js` 并且写入如下代码：

```
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
  ctx.response.body = 'Hello World';
};

app.use(main);
app.listen(3000);
```

我们通过 `node server.js` 来启动这个简易的后端，之后我们通过 `http://localhost:3000` 即可访问该服务。

上文中，我们仅仅使用了一个处理函数，我们利用的就是 Koa 提供的 Context 对象（即 ctx 参数），这个 Context 对象，表示一次请求响应的上下文，一般来说，我们可以通过 `Context.request` 来获取请求相关的内容，通过 `Context.response` 来获取响应相关的内容。

### 设置路由

我们发现，上面的例子中无论我们怎样输入路径都会返回 `Hello World`，这实际上是非常不实用的，实际上，我们可以使用 `Context.request.path` 来获取用户的请求路径，然后根据不同的路径设置返回不同的内容。

例如我们对上文的 `server.js` 做一点更改：

```
const Koa = require('koa');
const app = new Koa();

const main = ctx => {
  if (ctx.request.path !== '/') {
    ctx.response.body = 'Hello World';
  } else {
    ctx.response.body = 'Hello Index';
  }
};

app.use(main);
app.listen(3000);
```

我们再次使用 `node server.js` 运行，之后我们访问 `http://localhost:3000` 会返回 `Hello World`，而如果设置其他的路由，比如 `http://localhost:3000/other` 则会返回 `Hello Index`。

这便实现了我们对路由的初步管理。

### 使用 koa-route

在实际使用中，我们自己根据 `Context.request.path` 来判断还是有些麻烦，一般我们会使用 koa-route 来管理路由。

koa-route 的使用也很简单，我们可以再对代码稍作更改：

```
const Koa = require('koa');
const route = require('koa-route');
const app = new Koa();

const main = ctx => {
    ctx.response.body = 'Hello Index';
};

const world = ctx => {
 ctx.response.body = 'Hello World';
}

app.use(route.get('/', main));
app.use(route.get('/world', world));
app.listen(3000);
```

这个时候，我们访问 `http://localhost:/world` 和 `http://localhost`，会有对应的返回内容出现。

实际上，掌握以上内容就已经基本可以尝试 react 服务端渲染了，所以我们关于 koa 使用的介绍本次就到此为止，如果需要学习更多内容，可以进一步查阅更多资料。