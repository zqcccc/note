# CMD & SeaJS

CMD（Common Module Definition） 通用模块定义。其是国内前端大牛玉伯的杰作，而 SeaJS 则是 CMD 规范的模块加载器即具体实现。然而随着前端的发展，SeaJS 已经逐渐退出了前端的舞台了。因此这里只会稍微给大家讲解下 CMD 和 SeaJS 的一些知识点。大家了解下就好。

## CMD 和 AMD

通常我们会拿 CMD 规范来和 AMD 规范进行对比。那么 AMD 和 CMD 有什么区别和对比的呢？

### 申明依赖模块不同

对于依赖的模块，AMD 和 CMD 的处理方式是不一样的。

- AMD **推崇依赖前置**，在定义模块的时候就要声明其依赖的模块。
- CMD **推崇依赖就近**，只有在用到某个模块的时候再去 require 。

```js
// CMD
define(function(require, exports, module) {
  var a = require('./a')
  a.doSomething()
  // 依赖可以就近书写
  var b = require('./b') 
  b.doSomething()
})

// AMD 默认推荐的是 依赖必须一开始就写好
define(['./a', './b'], function(a, b) { 
  a.doSomething()
  b.doSomething()
}) 
```

### 执行依赖模块时机

- AMD 提前执行依赖（异步加载：依赖先执行）+延迟执行
- CMD 延迟执行依赖（运行到需加载，根据顺序执行）

### 加载器

AMD 是 `RequireJS` 在推广过程中对模块定义的规范化产出。
CMD 是 `SeaJS` 在推广过程中对模块定义的规范化产出。

## 使用SeaJS

SeaJS 是 CMD 规范的具体实现。使用 SeaJS 和使用 requireJS 十分类似，只是写法上稍微有所不同，具体如下：

1. 引入 SeaJS 的库
2. 定义模块（define）
3. 暴露模块接口（exports）
4. 加载依赖模块（require）

如下面的代码：

```js
<script src="./sea.js"></script>
<script>
  define(function (require,exports,module) {
      // exports : 对外的接口
      // requires : 依赖的模块
      require('./a.js');//如果地址是一个模块的话，那么require的返回值就是模块中的exports
  });
</script> 
```

## SeaJS（CMD）为什么被淘汰

事实上，在过去 SeaJS（CMD） 曾经颇具影响力，为什么要逐渐被淘汰了呢？

感兴趣的同学可以带着这样的问题，阅读下面的帖子。
[Sea.js作者发布微博: 应该给 Sea.js 和 KISSY 也树一块墓碑了。 为啥啊?过时了吗?](https://www.zhihu.com/question/34756861)

## 更多阅读

- [SeaJS 规范文档](https://github.com/seajs/seajs/issues/242)
- [从 CommonJS 到 Sea.js](https://github.com/seajs/seajs/issues/269)