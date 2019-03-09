# jqXHR

**jqXHR** 是一个对象，是 jQuery 中的 XHR 的对象，影响了一代人。

你辛辛苦苦的写了下面这个代码：

```js
var xhr = new XMLHttpRequest();

xhr.onreadystatechange = () => {
    if (xhr.readyState === 4) {
        // todo
    } else {
        console.log(xhr.status);
    }
}

xhr.open('get', 'data.json');
xhr.send(null);
```

使用 jQuery 只用这样：

```js
$.getJSON('data.json', function() {
    // todo
});
```

我想没人不喜欢这样写吧，具体可以参考 <http://api.jquery.com/jquery.getjson/> 。所以很多人认为 [Fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API) 的出现就是受到 jQuery 的影响。

`$.getJSON` 是一个简写，jQuery 的 ajax 有更底层的接口：

```js
$.ajax({
    dataType: 'json',
   url: 'data.json',
   success: function() {
        // todo
    }
});
```

别忘了我们的题目是 jqXHR ，所以接口什么的不多说，更详细的介绍可以参考这里：<http://api.jquery.com/jQuery.ajax/> 。

给上面的代码赋个值：

```js
const jqXHR = $.ajax({
    dataType: 'json',
   url: 'data.json',
   success: function() {
        // todo
    }
});
```

没错，所有的 jQuery ajax 返回值，不管是 `ajax()` 底层还是 `getJSON()` 高阶，都是返回 jqXHR 对象。

jqXHR 是原生 XHR 的封装，原生有的它都有，原生没有的，它也有。

> The jqXHR objects returned by `$.ajax()` as of jQuery 1.5 implement the Promise interface, giving them all the properties, methods, and behavior of a Promise.

注意 **Promise** 字眼，Promise 是你们必须要掌握的东西，因为它是所有异步编程技术的基础，可以看看下面的参考资料。

而 Promise 带给 jqXHR 的变化，就是像下面这样写代码：

```js
$.getJSON()
 .done((data) => { // todo })
   .done((data) => { // todo again })
    .fail((err) => { // if error })
```

这就是为什么要聊聊 **jqXHR** 。

向 jQuery 致敬。

## 参考

- [JavaScript Promise迷你书（中文版）](https://www.gitbook.com/book/wohugb/promise/details)
- <http://api.jquery.com/jQuery.ajax/#jqXHR>