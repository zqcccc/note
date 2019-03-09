# ES7 的 Decorator

在许多面向对象的语言中都有**装饰器（Decorator）**，用来增加类的功能和职责。

令人喜悦的是，在未来的 JavaScript 中也引入了这个概念，目前 babel 对这个概念也有很好的支持。如果你是一个喜欢尝试新技术的开发者，可以借助 babel 来大胆使用它。下面让我们来了解一下更多内容。

## 如何使用

`Decorator` 在目前还只是一个提议。如果你现在就想要体验使用的话，可以通过下面两种方法：

### 通过使用 `Babel` 来转换使用

1. 首先全局安装babel组件模块

   ```shell
   npm install babel -g
   ```

2. 命令行开启

    

   ```
   Decorator
   ```

   ```shell
   babel --optional es7.decorators test.js > test.es5.js
   ```

### 通过 [在线的 REPL](http://babeljs.io/repl/) 提前体验

Babel 的官方网站提供一个在线转码器，其支持 Decorator 的在线转码。

## JavaScript 的 Decorator

**装饰器（Decorator）** 在现代编程语言中是相当普遍的。事实上，JavaScript 装饰器的语法与 Python 装饰器的语法非常相似。有以下两种用法：

- 装饰类
- 装饰类的方法

## 装饰类

```js
@superManPower
class Man {
  // some code
}

// 装饰器函数
function superManPower(target) {
  target.super = true;
}

// 等同于
class Man {}
// 装饰器函数 superFunc
function superManPower(target) {
  this.super = true;
}
Man = superManPower(Man) || Man;
```

上面是简单的一个 **装饰器（Decorator）** 的例子，其中

- `@superManPower` 则为使用**装饰器 superManPower**

- **装饰器 superManPower** 修改了类 `Man` 的行为，给其加上静态属性 `super`

- 此时装饰器函数是一个对类进行处理的函数。其第一个参数为需要被装饰的目标类，如下：

  ```js
  // 装饰器函数
  function superManPower(target) {
    // target 即为被装饰的目标
    target.super = true;
  }
  ```

## 装饰类的方法

装饰器除了可以装饰我们的类之外，还可以修饰类的属性方法。如下所示：

```js
function superInitPower(attack, defense) {
  return function(target, key, descriptor) {
    // 保存方法
    const method = descriptor.value;
    // 修改方法
    descriptor.value = (...args)=>{
      // 增加攻击力和防御力
      args[0] += 100;
      args[1] += 100;
      // 调用原本的方法
      return method.apply(target, args);
    }
  return descriptor;
  }
}
class Man {
  @superInitPower
  init(attack, defense) {
    this.attack = attack; // 初始攻击力
    this.defense = defense; // 初始攻击力
  }
}
```

此时，装饰器函数可以接受三个参数，和我们的 [Object.defineProperty()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 的参数基本保持一致，分别表示如下：

- 第一个参数 `target` 表示修饰的目标对象 即类的实例

  > 这不同于之前装饰类，之前装饰类 `target` 表示的是类本身

- 第二个参数 `key` 表示需要装饰的属性名

- 第三个参数 `descriptor` 是该属性的描述对象

  ```js
  // descriptor 对象的值
  {
    value: specifiedFunction, // 指定的方法
    enumerable: false, // 是否可遍历迭代
    configurable: true, 
    writable: true
  };
  ```

## 多层装饰器

我们可以同时使用多个装饰器，如下所示：

```js
@healthy
class Person {
  @runFase
  @eatHuge
  init() { 
  }
}
```

例如上面的，我们使用了多个装饰器，增加类和类的方法的职责。

## 更多参数

上面我们看到装饰器参数都是固定的，对于一些场景可能会不够用。此时我们通过包装一层函数的方式来增加参数，如下所示：

```js
// 装饰器函数 - 增加参数
function superFunc(num, canUse) {
  return function(target) {
    target.canUse = canUse;
    target.num = num;
  }
}

// 因此下面我们就可以这样调用
@superFunc(1, true)
class Base1() {}
Base1.canUse; // true
Base1.num; // 1

@superFunc(2, false)
class Base2() {}
Base2.canUse; // false
Base2.num; // 2
```

上面中，我们看到装饰器 `superFunc` 是可以接受参数，通过各种参数来增强装饰器的能力。

```
注: 装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。这意味着，装饰器本质就是编译时执行的函数。
```

### 不能装饰普通函数

事实上 **装饰器（Decorator）** 只使用与装饰类和类的方法，不能使用于普通函数。这是因为变量提升会产生系列的问题。这里大家可以查看更多阅读来了解相关的问题详情。

## 使用场景

我们常常使用 **装饰器（Decorator）** 来实现 [AOP(面向切面编程)](http://qingbob.com/aop-programming/)。例如前面我们习题讲到的日志，也是非常经典的应用：[日志系统]。(<http://taobaofed.org/blog/2015/11/16/es7-decorator/#4%E3%80%81%E7%BB%8F%E5%85%B8%E5%AE%9E%E7%8E%B0%EF%BC%9ALogger>)

## 更多阅读

想要了解更多知识，大家可以阅读以下的资料文献。

- [javascript-decorators](https://github.com/wycats/javascript-decorators)
- [Exploring EcmaScript Decorators](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841)
- [ECMAScript Proposal for JavaScript Decorators
  (and protocols)](https://ponyfoo.com/articles/javascript-decorators-proposal)
- [ECMAScript 6 入门 - 阮一峰](http://es6.ruanyifeng.com/#docs/decorator)