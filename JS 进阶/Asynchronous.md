# 单线程

JavaScript 语言的一大特点就是**单线程**，不是多线程的原因是，js 主要用途是与用户交互，以及操作 DOM。这决定了它只能是单线程，不然会带来很复杂的同步问题（如果是多线程应该要有死锁机制）。

# 任务队列

单线程就需要任务队列，因为IO设备（输入输出设备）很慢（比如Ajax操作从网络读取数据），这时主线程完全可以不管IO设备，挂起处于等待中的任务，先运行排在后面的任务。等到IO设备返回了结果，再回过头，把挂起的任务继续执行下去。

于是，所有任务可以分成两种，一种是**同步任务**（synchronous），另一种是**异步任务**（**asynchronous**）。

**同步任务**指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；**异步任务**指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

![任务队列](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100801.jpg)

"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。但是，由于存在后文提到的"定时器"功能，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。

# Node.js的Event Loop

Node.js的运行机制如下。

1. V8引擎解析JavaScript脚本。
2. 解析后的代码，调用Node API。
3. [libuv库](https://github.com/joyent/libuv)负责Node API的执行。它将不同的任务分配给不同的线程，形成一个Event Loop（事件循环），以异步的方式将任务的执行结果返回给V8引擎。
4. V8引擎再将结果返回给用户。

process.nextTick（我的理解就是在同步任务的最后执行）方法可以在当前"执行栈"的尾部----下一次Event Loop（主线程读取"任务队列"）之前----触发回调函数。也就是说，它指定的任务总是发生在所有异步任务之前。setImmediate（这个就不一定了，setImmediate 递归调用的时候一定会在 setTimeout 的前面）方法则是在当前"任务队列"的尾部添加事件，也就是说，它指定的任务总是在下一次 Event Loop 时执行，这与 setTimeout(fn, 0) 很像。

多个process.nextTick语句总是在当前"执行栈"一次执行完，多个setImmediate可能则需要多次loop才能执行完。事实上，这正是Node.js 10.0版添加setImmediate方法的原因，否则像下面这样的递归调用process.nextTick，将会没完没了，主线程根本不会去读取"事件队列"！

**以上内容来自阮一峰的个人博客<http://www.ruanyifeng.com/blog/2014/10/event-loop.html>**

需要一个本地服务器

http-server 静态服务器

## AJAX

Asynchronous Javascript + XML 所以叫 AJAX，不过现在数据格式一般是 json

```javascript
function getData(callback) {
    var xhr = new XMLHttpRequest()
    
    xhr.onreadystatechange = () => { //监听请求状态
        console.log('xhr after response', Object.assign(xhr))
        
        if(xhr.readyState === 4) {
            callback(JSON.parse(xhr.responseText), xhr) // JSON.parse反序列化字符串
        } else {
            console.log(xhr.status)
        }
    }
    
    xhr.open('get', 'data.json') // 打开连接
    xhr.send(null) // 发送请求
}
```

- `new XMLHttpRequest()` 新建连接
- `xhr.open()` 打开连接
- `xhr.send()` 发送数据
- 接收数据

监听 xhr.readyState

- 新建连接 0
- 打开连接 1
- 发送数据 2
- 接受数据 3 是开始接受，4 是接受完成

# JSON

> 前端新手对 JSON 的理解都是错误的。 — 让人头疼的小江

官网的第一句话：

> **JSON** (JavaScript Object Notation) is a lightweight data-interchange format.

JSON 是一种轻量级的数据交换格式。JSON 是一种格式，而不是对象，所以别再说“这个 JSON 对象有问题”这种话。

```js
var a = { // 这是一个对象字面量，和 JSON 无关
    "hello": "JSON"
};

var b = [ // 这是一个数组字面量，和 JSON 无关
    {
        "hello": "JSON"
    }
];

var c = '{"hello":"JSON"}'; // 这是一个字符串变量，和 JSON 有一点关系
{"hello":"JSON"}
```

这才是 JSON ，JSON 就是文本，和什么语言没有关系，JS 可以解析，自然 JAVA、Python 都可以解析，所以`{"hello":function () {}}`不是一个 JSON 是不是就很容易理解了，你这个 `function`放在 JAVA 里面算个什么回事？！

当然，它是有格式的文本，至于什么格式，看官网 <http://json.org/> 就好了。

“让人头疼的小江大哥，后台同学返回的 JSON 数组有问题啊。”

“ (⊙ˍ⊙)”

## 同源策略

same-origin policy

**不同域**的客户端**脚本**在没有明确授权的情况下，不能**读写**对方的**资源**

同协议、同域名、同端口

### CORS

Cross-Origin Resource Sharing 跨域资源共享

有浏览器兼容问题

### JSONP

JSON with Padding

就是用 `script` 标签

缺点也很明显，只能是 get 请求