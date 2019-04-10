[class v3 version](https://frontendmasters.com/courses/deep-javascript-v3/)

[ECMA-262 specification](https://tc39.github.io/ecma262/)

Kyle Simpson

## Type

### Primitive Types

"In JavaScript, everything is an object" 这句话是错的，但是人们这么说的原因是因为，大多 js 中的变量都表现的像一个对象

**The ECMAScript language types are Undefined, Null, Boolean, String, Symbol, Number, and Object.**(官方定义原话)

在 js 中，变量没有类型，值才有

### undefined vs undeclared vs uninitialized (aka TDZ)

TDZ aka temporal dead zone

### NaN

0 不应该是缺少数字时的表示

```js
typeof NaN // "number"
```

NaN 更准确的理解不应该是 not a number，而是 invalid number 无效数字，无效数字也是数字

isNaN 会先将参数强制转换为数字再去判断是不是 NaN

```js
isNaN('hahah') // true
// ES6 提供了更完备的 isNaN
Number.isNaN('hahah') // false
```

### Negative Zero

```js
var trendRate = -0
trendRate === -0 // true

trendRate.toString() // "0"
trendRate === 0 // true
trendRate < 0 // false
trendRate > 0 // false

Object.is(trendRate, -0) // true
Object.is(trendRate, 0) // false
```

```js
Math.sign(-0) // -0
Math.sign(0) // 0
// Math.sign 不能判断 0 的正负，我们可以优化一下
function sign(n) {
    return n !== 0 ? Math.sign(n) : Object.is(n, -0) ? -1 : 1
} // !== 和 === 也不能判断正负0，Object.is 可以判断
```

### Fundamental Objects

如果要基本对象的时候应该用 new

- Object()
- Array()
- Function()
- Date()
- RegExp()
- Error()

这些就不要用 new 了，直接用就行了：

- String()
- Number()
- Boolean()

## Coercion

### toString

这是这个方法一些比较奇怪的地方

```js
(-0).toString() // "0"，这个就不是我们想要的
([]).toString() // ""，它去掉了空数组的中括号
([1, 2, 3]).toString() // "1,2,3"
([null, undefined, null]).toString() // ",,"
([[],[],[]]).toString() // ",,"
([,,,,,]).toString() // ",,,,"

({}).toString() // "[object Object]"
({a:2}).toString() // "[object Object]"
({toString(){return 'x'}}).toString() // "x"，重写 toString 方法
```

### toNumber

```js
+"" // 0
+null // 0

+[] // 0
+[""] // 0
+[null] // 0
+[undefined] // 0
+[[[]]] // 0

+{} // NaN
+{valueOf(){return 123}} // 123
```

### toBoolean

falsy:

- ""
- 0, -0
- null
- NaN
- false
- undefined

### Boxing

```js
if (studentNameElem.value.length > 50) { // 这里有隐含的转换，它让一个字符串表现的像对象一样，有length，Kyle Simpson 称之为 Boxing
    console.log("Student's name too long")
}
```

所有语言都有类型转换，因为这是绝对必要的，在 JavaScript 中你也必须要用，你没得选

conversion aka coercion

### Corner Cases

只要有类型转换就会有 Corner case 即极端情况

