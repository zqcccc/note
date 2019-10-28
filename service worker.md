## **Web workers**

Web workers 是最常用的 worker 类型。它不像另外两种，它们除了运行在主进程外的特性外，没有一个特殊的应用场景。所以，web worker 可以用于减少主进程上大量的进程活动。

```js
/* main.js */
const myWorker = new Worker('worker.js');
```

**worker.js** 文件中的任何代码都会开始运行，web worker 最有用的是用于减少那些可能需要花费很长时间的工序或者与其他进程上平行运行。

一个很好的例子是图片进程web应用：[Squoosh](https://link.juejin.im/?target=https%3A%2F%2Fsquoosh.app%2F)，它使用 web worker 来处理图片处理任务，可以让主进程有精力处理用户与应用的交互而不被打扰。

像所有的 worker，web worker 没有获取 DOM 的权限，这意味着任何需要的信息将被在 worker 和主脚本间传递，传递参数使用 **window.postMessage()**。

```js
/* main.js */

// 创建 worker
const myWorker = new Worker('worker.js');

// 向 worker 传递信息
myWorker.postMessage('Hello!');

// 接收从 worker 传递过来的信息
myWorker.onmessage = function(e) {
  console.log(e.data);
}
```

在worker脚本中，我们可以监听主脚本中的消息，并将响应返回。

```js
/* worker.js */

// 接收主文件的信息
self.onmessage = function(e) {
  console.log(e.data);

  // 向主文件发送信息
  self.postMessage(workerResult);
}
```

## **Service workers**

Service workers 是一种提供详细的作为浏览器和网络或缓存间的代理的服务。

与 Web worker 类似，service worker 是在主脚本文件中注册，引用到一个专门的 service worker 文件。

```js
/* main.js */
navigator.serviceWorker.register('/service-worker.js');
```

与一般的web worker不同，service worker有一些额外的特性来实现代理的目的。只要它们被安装且被激活，service worker就可以拦截主文档中发起的任何网络请求。

```js
/* service-worker.js */

// Install （安装）
self.addEventListener('install', function(event) {
    // ...
});

// Activate （激活）
self.addEventListener('activate', function(event) {
    // ...
});

// 监听主文档中的网络请求
self.addEventListener('fetch', function(event) {
    // ...
});
```

只要一被拦截，service worker就可以返回一个缓存中的文档作为响应，而不用走网络请求，因此可以让应用离线运行。

```js
/* service-worker.js */

self.addEventListener('fetch', function(event) {
    // 返回缓存中的数据
    event.respondWith(
        caches.match(event.request);
    );
});
```

## **Worklets**

Worklet 是一个非常轻量且高度特别的worker。它可以让开发者在浏览器渲染进程中的多个部分做钩子，可以让我们有浏览器渲染进程中底层的权限，比如样式和布局。