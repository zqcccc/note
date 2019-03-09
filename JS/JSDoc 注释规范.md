# JSDoc 注释规范

## 什么是 JSDoc

JSDoc 是一个根据 JavaScript 文件中注释信息，生成 JavaScript 应用程序或模块的API文档的工具。你可以使用 JSDoc 标记如：**命名空间**，**类**，**方法**，**方法参数**等。从而使开发者能够轻易地阅读代码，掌握代码定义的类和其属性和方法，从而降低维护成本，和提高开发效率。

## JSDoc 注释规则

JSDoc注释一般应该放置在方法或函数声明之前，它必须以`/ **`开始，以便由JSDoc解析器识别。其他任何以`/*`，`/***`或者超过3个星号的注释，都将被JSDoc解析器忽略。如下所示：

```javascript
/**
* 一段简单的 JSDoc 注释。
*/
```

## JSDoc 的注释效果

假如我们有一段这样的代码，没有任何注释，看起来是不是有一定的成本。

```javascript
function Book(title, author) {
    this.title=title;
    this.author=author;
}
Book.prototype={
    getTitle:function(){
        return this.title;
    },
    setPageNum:function(pageNum){
        this.pageNum=pageNum;
    }
};
```

如果使用了 JSDoc 注释该代码后，代码的可阅读性就大大的提高了。

```javascript
/**
 * Book类，代表一个书本.
 * @constructor
 * @param {string} title - 书本的标题.
 * @param {string} author - 书本的作者.
 */
function Book(title, author) {
    this.title=title;
    this.author=author;
}
Book.prototype={
    /**
     * 获取书本的标题
     * @returns {string|*} 返回当前的书本名称
     */
    getTitle:function(){
        return this.title;
    },
    /**
     * 设置书本的页数
     * @param pageNum {number} 页数
     */
    setPageNum:function(pageNum){
        this.pageNum=pageNum;
    }
};
```

## @constructor 构造函数声明注释

`@constructor` 明确一个函数是某个类的构造函数。

## @param 参数注释

我们通常会使用 `@param` 来表示函数、类的方法的参数，`@param` 是JSDoc中最常用的注释标签。参数标签可表示一个参数的**参数名**、**参数类型**和**参数描述**的注释。如下所示：

```js
/**
 * @param {String} wording 需要说的句子
 */
function say(wording) {
  console.log(wording);
}
```

## @return 返回值注释

`@return` 表示一个函数的返回值，如果函数没有显示指定返回值可不写。如下所示：

```js
/*
 * @return {Number} 返回值描述
 */
```

## @example 示例注释

`@example` 通常用于表示示例代码,通常示例的代码会另起一行编写，如下所示：

```js
/*
 * @example
 * multiply(3, 2); 
 */
```

## 其他常用注释

- `@overview` 对当前代码文件的描述。
- `@copyright` 代码的版权信息。
- `@author <name> [<emailAddress>]` 代码的作者信息。
- `@version` 当前代码的版本。

## 更多参考

如果想了解更多的 JSDoc 注释的内容，可参考下面的链接。

- [JSDoc 文档](http://usejsdoc.org/index.html)