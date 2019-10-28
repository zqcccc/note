# ES6 的继承

到目前为止，我们已经知道了 JS 中继承方式的最佳实践了，相信大家会有这样的疑惑：怎么感觉 JS 继承方式这么绕，又要借用构造函数，又要自己封装继承原型的函数。没错，你们的感觉是对的，大家也是这么觉得的，以至于后来有了很多 JS 继承的封装或者语法糖，比如现在比较火的 [`TypeScript`](https://www.typescriptlang.org/) 就是参照经典的面向对象对 JS 的类的声明和继承进行了封装。

幸运的是，ES6 标准已经将 经典的 `class` 声明类和继承的方式纳入标准了，前面经典的面向对象扩展资料中就有提到 ES6 中怎么声明类了，下面是使用 ES6 实现 `Person` 类。

```js
//定义类
class Person {
    // 构造函数
    constructor(name) {
        this.name = name;
    }

    // 方法
    sayName() {
        console.log(this.name);
    }
}
```

在 ES6 中继承是通过 `extends` 关键字声明的，比如下面 `Student` 类继承了 `Person` 类

```js
//定义类
class Student extends Person {
    // 构造函数
    constructor(name, grade) {
        super(name);
        this.grade = grade;
    }

    // 方法
    sayGrade() {
        console.log(`I am Grade ${this.grade}`);
    }
}
```

上面继承方式关键点其实就2个，一个是通过 `extends` 关键字声明继承关系，第二是在子类构造函数 `constructor` 中调用 `super` 函数，这其实就相当于我们的借用构造函数。要注意的点是 `constructor` 中 `this` 对象要 `super` 调用之后使用，不然会报错。具体原因可以看下面参考资料。

目前部分现代浏览器新版本已经实现对 ES6 中的 `class` 和继承的支持，但是注意在旧版本或者 IE 浏览器中是不支持的，所以使用的时候要注意，或者配合使用 [Babel](https://babeljs.io/) 等编译工具。

------

- [Class 的继承](http://es6.ruanyifeng.com/#docs/class-extends)