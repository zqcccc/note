# JavaScript 性能优化

语言层面的性能优化

- 内存管理
- 垃圾回收与常见 GC 算法
- V8 引擎的垃圾回收
- Performance 工具

## JavaScript 内存管理

memory management

- 内存：由可读写单元组成，表示一片可操作空间
- 管理：认为的去操作一片空间的申请、使用和释放
- 内存管理：开发者主动申请空间、使用空间、释放空间

ECMAScript 没有提供相应的操作 api，所以 JavaScript 不能像 C、C++ 那样主动的去动用 API 去操作内存空间

```js
// 申请
let obj = {};
// 使用
obj.name = "obj";
// 释放
obj = null;
```

## JavaScript 垃圾回收

- JavaScript 中内存管理是自动的
- 对象不再被引用时是垃圾
- 对象不能从根上访问到时是垃圾

可达对象

- 可以访问到的对象就是可达对象（引用、作用域链）
- 可达的标准就是从根出发是否能够被找到
- JavaScript 中的根就可以理解为是全局变量是对象

当没有路径能够找到这个对象的时候，这个对象就会被当成垃圾回收

## GC 算法

Garbage Collection，GC 可以找到内存中的垃圾、并释放和回收空间，算法就是工作时查找和回收所遵循的规则

```js
// 程序中不再需要使用的对象
function func() {
	name = 'lg'
	return `${name} is a coder`
}
func()

// 程序中不能再访问到的对象
function func() {
  const name = 'lg'
  return `${name} is a coder`
}
func
```

常见术语

- 引用计数
- 标记清除
- 标记整理
- 分带回收

## 引用计数算法实现原理

- 核心思想：设置引用树，判断当前引用数是否为 0
- 引用计数器
- 引用关系改变时修改引用数字
- 引用数字为 0 时立即回收

### 引用计数算法优缺点

**优点**

- 发现垃圾时立即回收
- 最大限度减少程序暂停
  - 程序执行必然有内存的消耗，内存终归是有上限，总归有占满内存的时候，当内存即将占满的时候，算法就被触发去释放内存，这样来达到减少程序暂停

**缺点**

- 无法回收循环引用的对象
- 时间开销大
  - 引用计数需要去维护数值的变化，对象是很多的，那耗时就也不小，这个耗时是相对于其他算法而言的

## 标记清楚算法

- 核心思想：分标记和清除两个阶段完成
- 遍历所有对象找标记活动对象
- 遍历所有对象清除没有标记对象
- 回收相应的空间

标记阶段就是先从根去查找可达对象，如果有嵌套的对象，就递归查找，然后标记对象，在清除阶段就清除回收没有标记的对象，同时清除所有标记

回收的空间会被放在空闲链表上，方便后续程序在这申请空间使用

### 标记清楚算法优缺点

**优点**

- 解决循环引用的问题（相对引用计数算法而言）

**缺点**

- 空间碎片化
  - 空间都有一部分来存储元信息（数据大小，内存地址等）的，叫“头”，一部来存储数据的，叫“域”，在回收的时候，内存空间的地址是不连续的，这样回收的空间是在各个不同的角落的，大小也不一样，如果需要申请新的内存空间的话，空闲链表中没有一样大小的空间的话，就不太合适复用
- 不会立即回收垃圾对象，清除过程其实程序是停止工作的

## 标记整理算法

- 标记整理可以看做是标记清除的增强
- 标记节点的操作和标记清除算法一致
- 清除阶段会先执行整理，移动对象位置

在清除之前先整理，把不活动的内存空间整理成一段连续的空间，然后再回收空间，这样得到的连续空间就不会有碎片化的问题

缺点也是不会立即回收垃圾对象

## V8

- V8 是最主流的 JavaScript 执行引擎
- V8 采用即时编译
- V8 内存设限 (64 位系统不超过 1.5g，32 位系统不超过 800m)
  - V8 本来是为了浏览器而做的，这样的内存够用了

### V8 如何回收新生代对象

V8 是基于分代的垃圾回收思想设计的，所以在 V8 内部是把内存分成了两个部分，一部分用来存储新生代对象，一部分用来存储老生代对象

- V8 内存空间一分为二
- 小空间用于存储新生代对象 (32M - 64 位 | 16M - 32 位)
- 新生代指的是存活时间较短的对象

新生代对象回收实现

- 回收过程采用复制算法 + 标记整理
- 新生代内存区分为二个等大小空间
- 使用空间为 From，空闲空间为 To
- 活动对象存储于 From 空间
- GC 开始，标记整理后将活动对象拷贝至 To (整理复制成连续空间)
- From 与 To 交换空间完成释放

细节说明

- 拷贝过程可能出现晋升
- 晋升就是将新生代对象移动到老生代
  - 一轮 GC 还存活的新生代需要晋升
  - To 空间的使用率超过 25%
    - To 会再次变成 From 的，那下一轮新生代进来的多了，这样 From 空间就不够用了，所以需要晋升

### V8 如何回收老生代对象

- 老生代对象存放在右侧老生代区域
- 64 位操作系统 1.4g，32 位操作系统 700m
- 老生代对象就是指存活时间较长的对象

回收实现

- 主要采用标记清除、标记整理、增量标记算法
- 首先使用标记清除完成垃圾空间的回收 (主要是这种，快)
- 采用标记整理进行空间优化 (新生代需要往老生代晋升时空间不足的时候使用)
- 采用增量标记进行效率优化

细节对比

- 新生代区域垃圾回收使用空间换时间 (因为空间分配少，时间提升却很大，所以效率还是不错的)
- 老生代区域垃圾回收不适合复制算法 (老生代空间区域大，对象数据也多)

## Performance

- GC 的目的是为了实现内存空间的良性循环
- 良性循环的基石是合理使用
- 时刻关注才能确定是否合理
- Performance 提供多种监控方式

通过 Performance 时刻监控内存，使用步骤

- 开发人员工具面板，性能
- 开启录制
- 访问具体网址
- 执行用户行为，一段时间后停止录制
- 分析性能界面中记录的内存信息

### 内存问题的体现

以下问题都是在网络正常的情况下

- 页面出现延迟加载或经常性的暂停
- 页面持续出现糟糕的性能
- 网页的性能随时间延长越来越差

### 监控内存的方式

内存问题的标准

- 内存泄漏：内存使用持续升高
- 内存膨胀：在多数设备上都存在性能问题
- 频繁垃圾回收：通过内存变化图进行分析

监控方式

- 浏览器任务管理器 (能知道页面有没有问题，但是不能定位那哪个脚本的问题)
  - 内存是 DOM 内存，JavaScript 实时内存才是我们要关注的
- TimeLine 时序图记录
- 堆快照查找分离 DOM
  - 分类 DOM 是声明了 DOM，但并没有在界面上去使用
- 判断是否存在频繁的垃圾回收
  - Timeline 中频繁的上升下降
  - 任务管理器中数据频繁的增加减小

分离 DOM

- 界面元素存活在 DOM 树上
- 垃圾对象的 DOM 节点
- 分离状态的 DOM 节点

为什么要确定频繁垃圾回收

- GC 工作时应用程序是停止的
- 频繁且过长的 GC 会导致应用假死
- 用户使用中感知应用卡顿

## 优化代码

精准测试 JavaScript 性能

- 本质上是采集大量的执行样本进行数学统计和分析
- 使用基于 Benchmark.js 的 ~~https://jsperf.com/ (不再维护)~~ 完成
- 网站 https://jsbench.me 做性能测试，浏览器也是多线程的，使用的时候只开一个标签页，这样测试的分配的资源就会比较多，在测试的时候也不要做别的，系统也是可以多线程的，这样的话我们的页面有可能会是挂起的状态从而会影响我们的测试结果
- 类似的工具还有很多

### 慎用全局变量

- 全局变量定义在全局执行上下文，是所有作用域链的顶端 (局部作用域找不到的变量就不停往外找，增加了查找的时间)
- 全局执行上下文一直存在于上下文执行栈，直到程序退出 (不利于 GC)
- 如果某个局部作用域出现了同名变量则会遮蔽或污染全局 (需要考虑同名问题)

```js
// bad
var i,
  str = "";
for (i = 0; i < 1000; i++) {
  str += i;
}

// good 这个比上面那个快很多
for (let i = 0; i < 1000; i++) {
  let str = "";
  str += i;
}
```


### 缓存全局变量

将使用中无法避免的全局变量缓存到局部

```js
// 假设页面中有很多个元素需要获取
function getElems() {
  let a = document.getElementById("a");
  let b = document.getElementById("b");
  let c = document.getElementById("c");
  let d = document.getElementById("d");
  let e = document.getElementById("e");
}
function getElems() {
  let docu = document;
  let a = docu.getElementById("a");
  let b = docu.getElementById("b");
  let c = docu.getElementById("c");
  let d = docu.getElementById("d");
  let e = docu.getElementById("e");
}
// 下面的会比上面的有更好的性能优势
```

### 通过原型新增方法

在原型对象上新增实例对象需要的方法

```js
var fn1 = function () {
  this.foo = function () {
    console.log(1111);
  };
};
let f1 = new fn1();

var fn2 = function () {};
fn2.prototype.foo = function () {
  console.log(1111);
};
let f2 = new fn2();
// 下面的会比上面的有更好的性能优势
```

### 避开闭包陷阱

- 外部具有指向内部的引用
- 在“外”部作用域访问“内”部作用域的数据

```js
function foo() {
  var name = "lg";
  function fn() {
    console.log(name);
  }
  return fn;
}
var a = foo();
a();
```

- 闭包是一种强大的语法
- 闭包使用不当很容易出现内存泄漏
- 不要为了闭包而闭包

```js
function foo() {
  var el = document.getElementById("btn"); // 这里的变量就是一直不会被♻️
  el.onclick = function () {
    console.log(el.id);
  };
}
foo();

function foo() {
  var el = document.getElementById('btn')
  el.onclick = function () {
    console.log(el.id);
  };
  el = null // 变量没用了就可以置空了，释放内存
}
```

### 避免属性访问方法使用

- JS 不需要属性的访问方法，所有属性在外部都是可见的
- 使用属性访问方法只会增加一层重定义，没有访问的控制力

```js
function Person() {
	this.name = 'icoder'
	this.age = 18
  this.getAge = function() { // 成员属性方法
    return this.age
  }
}
const p1 = new Person()
const a = p1.getAge()

// 下面的会比上面的快
function Person() {
	this.name = 'icoder'
	this.age = 18
}
const p1 = new Person()
const a = p1.age
```

### for 循环优化

```js
var aBtns = document.getElementsByClassName('btn')

for (var i = 0; i < aBtns.length; i++) {
  console.log(i)
}

// 下面比上面的性能更优
for (var i = 0, len = aBtns.length; i < len; i++) {
  console.log(i)
}
```

### 采用最优循环方式

```js
var arrList = [1, 2, 3, 4, 5]

arrList.forEach(function (item) {
  console.log(item)
})

for (var i = arrList.length; i; i--) {
  console.log(arrList[i])
}

for (var i in arrList) {
  console.log(arrList[i])
}
// foreach 最优，其次 for，最后 for in，后两者其实差异很小
```

### 文档碎片优化节点添加

节点的添加操作必然会有回流和重绘

```js
for (var i = 0; i < 10; i++) {
  var oP = document.createElement("p");
  oP.innerHTML = i;
  document.body.appendChild(oP);
}

// 下面的 DOM 操作更优
const fragEle = document.createDocumentFragment();
for (var i = 0; i < 10; i++) {
  var oP = document.createElement("p");
  oP.innerHTML = i;
  fragEle.appendChild(oP);
}
document.body.appendChild(fragEle);
```

### 克隆优化节点操作

```js
for (var i = 0; i < 10; i++) {
  var oP = document.createElement("p");
  oP.innerHTML = i;
  document.body.appendChild(oP);
}

// 下面的 DOM 操作更优
var old = document.getElementById("box");
for (var i = 0; i < 10; i++) {
  var newP = old.cloneNode("p");
  newP.innerHTML = i;
  document.body.appendChild(newP);
}
```

### 直接量替换 Object 操作

```js
var a1 = new Array(2)
a1[0] = 1
a1[1] = 2
a1[2] = 3

// 下面更优
var a = [1, 2, 3]
```

### 减少判断层级

```js
function doSomething(part, chapter) {
  const parts = [1, 2, 3, 4, 5];
  if (part) {
    if (parts.includes(part)) {
      console.log("属于当前数组");
      if (chapter > 5) {
        console.log("您需要提供 VIP");
      }
    }
  } else {
    console.log("请传入正确的内容");
  }
}
doSomething(5, 6);

// 下面的代码性能会更好
function doSomething(part, chapter) {
  const parts = [1, 2, 3, 4, 5];
  if (!part) {
    console.log("请传入正确的内容");
    return;
  }
  if (!parts.includes(part)) return;
  console.log("属于当前数组");
  if (chapter > 5) {
    console.log("您需要提供 VIP");
  }
}
doSomething(5, 6);
```

`if else` 更适合区间，`switch case` 更适合枚举

### 减少作用链查找层级

```js
var name = "xxx";
function foo() {
  name = "xxx666";
  function baz() {
    var age = 18;
    console.log(age);
    console.log(name);
  }
  baz();
}
foo();

// 下面的不需要到外层查找 name，性能更好，但是会占用更多空间
var name = "xxx";
function foo() {
  var name = "xxx666"; // 这里不一样
  function baz() {
    var age = 18;
    console.log(age);
    console.log(name);
  }
  baz();
}
foo();
```

### 字面量和构造式

```js
let test = () => {
  let obj = new Object()
  obj.name = 'xxx'
  obj.age = 38
  obj.slogan = '我为我而活'
  return obj
}

// 下面的会比较快，像 new Array，new String 这种更慢
let test = () => {
  let obj ={
    name:'xxx',
    age: 38,
    slogan: '我为我而活'
  }
  return obj
}
test()
```

### 减少循环体活动

能写多“少”就写多“少”

### 减少声明以及语句数

```js
let test = () => {
  var name = "xxx";
  var age = 38;
  var slogan = "我为我而活";
  return name + age + slogan;
};

let test = () => { // 这个执行更快，从编译的角度就可以解释，但是看起来比较乱
  var name = "xxx",
    age = 38,
    slogan = "我为我而活";
  return name + age + slogan;
};
test();

```

### 惰性函数与性能

```js
function addEvent(obj, type, fn) {
  if (obj.addEventListener) {
    obj.addEventListener(type, fn, false);
  } else if (obj.attachEvent) {
    obj.attachEvent("on" + type, fn);
  } else {
    obj["on" + type] = fn;
  }
}

function addEvent(obj, type, fn) { // 这种性能不一定高，要看场景
  if (obj.addEventListener) {
    addEvent = obj.addEventListener(type, fn, false);
  } else if (obj.attachEvent) {
    addEvent = obj.attachEvent("on" + type, fn);
  } else {
    addEvent = obj["on" + type] = fn;
  }
  return addEvent;
}
```

### 采用事件委托

减少大量事件注册

```html
<ul id="ul">
  <li>xixi</li>
  <li>xixi</li>
  <li>xixi</li>
</ul>

<script>
  var list = document.querySelectorAll("li");
  function showTxt(e) {
    console.log(e.target.innerHTML);
  }
  for (let item of list) {
    item.onclick = showTxt;
  }
  
  // 下面的的方法在需要绑定很多事件的时候性能会更好，这里的例子比较简单，可能得到的结果是上面的更快
  function showText(e) {
    var obj = e.target;
    if (obj.nodeName.toLowerCase() === "li")
      console.log(obj.innerHTML);
  }
  var oUL = document.getElementById("ul");
  oUL.addEventListener("click", showText, true);
  
</script>
```

