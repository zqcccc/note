# performance 详解

为了更加精确地测量和提高网页和Web应用程序的性能够，W3C 和各浏览器厂商共同制定了 **Web Performance API**。

我们可以通过该接口查看用户访问网站各项性能数据，如连接建立时间、dns时间等信息，为更好地增强网页性能提供了前所未有的支持。

## 浏览器内存相关 performance.memory

`performance.memory` 可获取浏览器的内存情况,这个属性并不是标准的performance 属性，只在 chrome浏览器中。具体有以下值：

- `usedJSHeapSize` 表示所有被使用的 js 堆栈内存
- `totalJSHeapSize` 表示当前 js 堆栈内存总大小
- `jsHeapSizeLimit` 表示内存大小限制

其中 `usedJSHeapSize` 不能大于 `totalJSHeapSize`，如果大于则可能出现了内存泄漏的情况。

## 网页导航相关 performance.navigation

`performance.navigation` 对象存储了两个属性，它们表示触发页面加载的原因。这些原因可能是页面重定向、前进后退按钮或者普通的 URL 加载。两个属性如下：

### performance.navigation.type

该属性的值为一个整数值，表示网页的加载来源，共以下4种情况。

| 数值 | 含义                                                     | 相应的常量        |
| ---- | -------------------------------------------------------- | ----------------- |
| 0    | 通过点击链接、地址栏输入、表单提交、脚本操作等方式加载。 | TYPE_NAVIGATENEXT |
| 1    | 通过刷新操作或者 location.reload() 方法                  | TYPE_RELOAD       |
| 2    | 通过历史遍历操作加载                                     | TYPE_BACK_FORWARD |
| 255  | 其他来源的加载                                           | TYPE_UNDEFINED    |

### performance.navigation.redirectCount

该属性表示到达当前页面，经过重定向的次数。

## 时间相关 performance.timing

`performance.timing` 对象包含了各种浏览器性能相关的信息如网站建立连接、DNS查询、DOM 解析等各项数据，使我们能够更为全面精确地了解网页性能的详细情况。

![img](http://coding.imweb.io/img/p6/performance.png)

以下是相关的属性：

- **navigationStart**：当前浏览器窗口的前一个网页关闭，发生unload事件时的时间。如果没有上一个页面，这个值会和 `fetchStart` 相同。通常我们也理解为准备加载新页面的起始时间。
- **redirectStart**：到当前页面的重定向开始的时间。当重定向的页面来自同一个域时这个属性才会有值，否则值为0。
- **redirectEnd**：到当前页面的重定向结束的时间。当重定向的页面来自同一个域时这个属性才会有值，否则值为0。
- **fetchStart**：准备使用HTTP请求(fetch)页面的时间。
- **domainLookupStart**：域名查询开始的时间。
- **domainLookupEnd**：域名查询结束的时间。
- **connectStart**：返回HTTP请求开始向服务器发送的时间,如果使用持久连接（persistent connection），则返回值等同于 `fetchStart` 的值。
- **(secureConnectionStart)**：可选特性。如果页面是HTTPS协议，则返回开始SSL握手的那个时间。如果当前网页不要求安全连接，则返回0。
- **connectEnd**：返回浏览器与服务器之间的连接建立的时间。如果建立的是持久连接，则返回值等同于 `fetchStart` 属性的值。连接建立指的是所有握手和认证过程全部结束。
- **requestStart**：返回浏览器向服务器发出HTTP请求时（或开始读取本地缓存时）的时间。
- **responseStart**：返回浏览器从服务器收到（或从本地缓存读取）第一个字节时的时间。
- **responseEnd**：返回浏览器从服务器收到（或从本地缓存读取）最后一个字节时的时间。
- **unloadEventStart**：返回同一个域名前一个网页的 `unload` 事件触发时的时间。否则返回值为0。
- **unloadEventEnd**：返回同一个域名前一个网页的 `unload` 事件触发时的时间。否则返回值为0。
- **domLoading**：返回当前网页 DOM 结构开始解析时（即 Document.readyState 属性变为 `loading`、相应的readystatechange事件触发时）的时间
- **domInteractive**：返回当前网页DOM结构结束解析、开始加载内嵌资源时（即Document.readyState属性变为 `interactive` 、相应的readystatechange事件触发时）的时间。
- **domContentLoadedEventStart**：返回当解析器发送 `DOMContentLoaded` 事件的开始时间
- **domContentLoadedEventEnd**：返回当文档的 `DOMContentLoaded` 事件的结束时间。
- **domComplete**：返回当前文档解析完成，即Document.readyState 变为 `complete` 且相对应的readystatechange 被触发时的时间。
- **loadEventStart**：返回该文档下，`load` 事件被发送时的时间。如果这个事件还未被发送，它的值将会是0。
- **loadEventEnd**：返回当 `load` 事件结束，即加载事件完成时的时间。如果这个事件还未被发送，或者尚未完成，它的值将会是0。

## 计算相关节点

我们可以通过上面的属性计算出许多网页的信息。

### 页面经历了多长时间

如下面代码表示距离浏览器开始加载网页到现在的时间间隔。

```js
var timing = performance.timing; 
var duration = Date.now() - timing.navigationStart; 
```

### 网页加载整个过程的(onload)耗时

如下面代码表示网页加载整个过程的耗时。

```js
var timing = performance.timing; 
var pageLoadTime = timing.loadEventEnd - timing.navigationStart;
```

### DNS 域名解析的耗时

如下面代码表示 DNS 域名解析的耗时。

```js
var timing = performance.timing; 
var dns = timing.domainLookupEnd - timing.domainLookupStart;
```

### tcp 连接的耗时

如下面代码表示 DNS 域名解析的耗时。

```js
var timing = performance.timing; 
var tcp = timing.connectEnd - timing.connectStart;
```

### TTFB 获取首字节的耗时

如下面代码表示 TTFB(time to frist byte ) 获取首字节的耗时。

```js
var timing = performance.timing; 
var ttfb = timing.responseStart - timing.navigationStart;
```

## 返回时间 performance.now()

`performance.now` 方法将会返回当前网页自从`performance.timing.navigationStart` 到当前时间之间的微秒数（毫秒的千分之一）。

performance.now() 返回的时间近似于 Date.now()。但前者的时间精度是后者的 1000 倍。

## 资源加载相关 performance.getEntries()

浏览器获取网页时，会对网页中每一个资源文件（js、css、图片等）发出一个HTTP请求。`performance.getEntries`会统计这些请求并且返回相应的时间信息。

如下所说，返回的是第二个HTTP请求的加载情况：
![img](http://coding.imweb.io/img/p6/performance-entries.png)

## 更多阅读

- [Window.performance - MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/performance)
- [Performance APIß](http://javascript.ruanyifeng.com/bom/performance.html#toc4)