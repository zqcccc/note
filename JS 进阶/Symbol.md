围绕原始数据类型创建一个显式包装器对象从ECMAScript 6开始不再被支持，所以`new Symbol()`会报错，然而，现有的原始包装器对象，如 `new Boolean`、`new String`以及`new Number`因为遗留原因仍可被创建


```js
Symbol([description])
Object.getOwnPropertySymbols(target);
```

当使用`JSON.strIngify()`时以`symbol`值作为键的属性会被完全忽略

Symbols在`for...in`迭代中不可枚举，如果想要达到效果，借助`Object.getOwnPropertySymbols(obj)`这个方法。

## 使用场景

1. 消除脏代码

```js
const tabTypes = {
    basic: Symbol(),
    super: Symbol(),
}

if (type === tabTypes.basic) { // 这里 type 只能是从 tabTypes 中取值才能是 true 了
    return <div>basic tab</div>
}

if (type === tabTypes.super) {
    return <div>super tab</div>
}
```

2. 作为对象属性 当一个复杂对象中含有多个属性的时候，很容易将某个属性名覆盖掉，利用 Symbol 值作为属性名可以很好的避免这一现象。

3. 模拟类的私有方法