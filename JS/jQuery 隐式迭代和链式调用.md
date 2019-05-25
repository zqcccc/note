# jQuery 隐式迭代和链式调用

学过 jQuery 之后，一般是不太有人再愿意写原生 JS 的，甚至有段时间 jQuery 成了 JS 的代名词。原因无他，足够简单方便。可在这简单方便的背后，可少不了两大功臣：隐式迭代和链式调用。

## 隐式迭代

对于原生 JS 来说，一般我们设置某类元素的样式，都得使用循环设置，而 jQuery 在使用的时候则无需考虑这点，简单示例如下：

```js
// 设置 .primary 元素的文字颜色为 #188eee

// 原生 JS
var primary = document.getElementsByClassName('primary');
for(var i = 0, len = primary.length; i < len; i++) {
    primary[i].style.color = '#188eee';
}

// jQuery
$('.primary').css('color', '#188eee');
```

这是因为 jQuery 的方法内部存在**隐式迭代**，它会对匹配到的所有元素进行循环遍历，执行相应的方法；无需我们再手动地进行循，方便我们使用。

除了隐式迭代外，jQuery 还提供了 [each](http://jquery.cuishifeng.cn/each.html) 方法，方便有需要的时候调用。比如要对每个元素做不同的处理：

```js
$("li").each(function(i){
   $(this).addClass('item-'+i);
 });
```

注：jQuery 还有一个全局的 each 方法，用于遍历对象或数组，具体可参考：[$.each](http://jquery.cuishifeng.cn/jQuery.each.html)

## 链式编程

从前我们要对某个元素进行一系列操作，只能一个一个来，而 jQuery 提供了链式操作，操作起来简直是不能再爽，如下：

```js
// 原始版
$('div').hide(); //隐藏页面上所有的div元素
$('div').text('new content'); //更新所有div元素内的文本
$('div').addClass("updatedContent"); //在所有的div元素上添加值为updatedContent的class属性
$('div').show(); //显示页面上所有的div元素

// 重写版，链式
$('div').hide().text('new content').addClass("updatedContent").show();

// 缩进版
$('div')
  .hide()
  .text('new content')
  .addClass("updatedContent")
  .show();
```

其原理就是每个方法的最后都返回了 this 对象，我们可以使用一份简单的代码演示下：

```js
// 定义类
function Person(opt) {
    this.name = opt.name;
    this.age = opt.age
}

// 定义 getName 方法
Person.prototype.getName = function() {
    console.log(this.name);
    return this; // 返回 this 对象
}

// 定义 sayHello 方法
Person.prototype.sayHello = function() {
    console.log('hello the world');
    return this; // 返回 this 对象
}

// 新建一个叫 next 的 Person 类
var next = new Person({
    name: 'next'
});

// 链式调用 getName 和 sayHello 方法
next.getName().sayHello(); 
```

更多关于 jQuery 的源码分析可参考：[jQuery源码分析系列](http://www.cnblogs.com/aaronjs/p/3279314.html)