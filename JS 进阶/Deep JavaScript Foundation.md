[class v2 version](https://frontendmasters.com/courses/deep-javascript-v3/)

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

```js
Boolean(new Boolean(false)) // true
```

## Philosophy of Coercion

JavaScript 的动态类型不是一种缺点，而是很强的一种能力

implicit is not magic 含蓄不是魔法

implicit is not bad

> "If a feature is sometimes useful and sometimes dangerous and if there is a better option then always use the better option."	-- "The Good Parts", Crockford

Useful: when the reader is focused on what's important

Dangerous: when the reader can't tell what will happen

Better: when the reader understands the code

## Equality

**== 和 === 在左右类型匹配的时候都是 ===**

== 的规则：

- 左右的 null undefined 是一样的，返回 true
- == 倾向于做数字的比较

- 一边是数字，一遍是字符串的情况下，会将字符串的那一遍变成数字
- 布尔也会变成数字类型
- 如果某一边不是字符串，数字和 symbol 而 typeof 判断是 object 的话，就会将这一边变成 primitive

### Double Equals Corner Cases

永远不要判断一个值和它取反的值是否相等，（不过判断 NaN 应该可以）

```js
[] == ![] // true
// [] == false
// "" == false
// 0 == false
// 0 == 0
// 然后就得到了 true
```

不要用 == 去判断 Boolean 的值

```js
var a = []
if (a) {} // 这样判断是比较合理的，在这里含蓄判断比明确的判断更好

if (a == false ) {} 
// 这里 a == false 
// 即 [] == false 
// 即 '' == false 
// 即 0 == 0 这个判断就是对的了
```

结论就是：== 要避免在以下情况中使用

- 判断 0 或者 "" ，"  "变成数字时也会变成 0
- 所有的非 primitive 类型
- == true，== false，判断布尔类型判断只用 === 

### The Case for Double Equals

== 不是用来比较不知道的类型的，而是用来比较知道类型的情况下的，且在 conversions 有用的情况下

因为 == 在左右两边类型都相等的情况下和 ===  是相等的，所以用 ===  是没有必要的

当左右两边的类型是不一样的时候，=== 永远都是 false

如果不知道类型，用 === 是最安全的，但在知道类型的情况下就应该用 ==

## Static Typing

TypeScript & Flow

### Inferencing

```js
var teacher = 'Kyle'

// .. 

teacher = { name: 'Kyle' }
// Error: can't assign object to string
```

Kyle 说他从没遇到过因为把一个数字赋值给一个曾经是字符串类型的变量会产生 bug，所以静态类型在 IDE 中给的报错提示对他来说是没有用的，但是他还说如果你因为给某个变量重新赋错了值而产生了 bug 的话，inferencing 会有用

inferencing 大概就是静态语言会去猜你应该保持的变量类型

### 自定义类型 Custom Types

```typescript
type student = { name: string }
function getName(studentRec: student): string {
    return studentRec.name
}

var firstStudent: student = { name: 'Frank' }

var firstStudentName: string = getName(firstStudent)
```

### Validating Operand Types

Kyle 认为这是静态类型被低估的一个优点，就是会检验操作符是否正确使用，而操作两个不同类型的数据是导致很多 bug 产生的原因，同时 Kyle 也希望如果 TypeScript有能控制是否允许类型强制变换的机制就好了，因为有时候我是希望有类型变换的，但有的时候我想尽量去避免，可惜的是，TypeScript并没有

总结就是：Kyle 认为 Typescript 太严格了，如果介于 JavaScript 和 TypeScript 就好了

### Understanding Your Types

  JavaScript  has a (dynamic) type system, which uses various forms of coercion for value type conversion, including equality comparisons. 

  However, the prevailing response seems to be: avoid as much of this system as possible, and use "===" to "protect" from needing to worry about types. Part of the problem with avoidance of whole swaths of JS, like pretending === saves you from needing to know types, is that it tends to systemically perpetuate bugs.

  You simply cannot write quality JS programs without knowing the types involved in your operations. Alternately, many choose to adopt a different "static types" system layered on top. While certainly helpful in some respects, this is "avoidance" of a different sort.

  Apparently. JS's type system is inferior so it must be replaced, rather than learned and leveraged

Many claim that JS's type system is  too difficult for newer devs to learn, and that static types are (somehow) more learnable

Kyle's claim: the better approach is to embrace and learn JS's type system, and to adopt a coding style which makes types as obvious as possible. By doing so, you will make your code more readable and more robust, for experienced and new developers alike.

As an option to aid in that effort, Kyle created Typl, which he believe embraces and unlocks the best parts of JS's types and coercion.

## Scope

许多人认为 JavaScript 是动态语言，于是觉得 JavaScript 就是解释性语言，应该是一行一行的执行的，其实 JavaScript 是编译语言，至少是它是会被解析的，在它被执行之前是会被处理的

### Dynamic Global Variables

```js
var teacher = "Kyle"

function otherClass() {
    teacher = "Suzy"
    topic = "React" // 它在 otherClass 里找不到 topic 就到外面去找，然后就自动生成了全局变量 topic，但这种行为只在不严格的情况下出现，
    console.log("Welcome!")
}

otherClass();

teacher;
topic;
```

## Scope & Function Expressions

### Function Expressions

```js
function teacher() {/* ... */}

var myTeacher = function anotherTeacher() {
    console.log(anotherTeacher)
}

console.log(teacher)
console.log(myTeacher)
console.log(anotherTeacher) // ReferenceError
```

声明表达式就像声明一个变量一样，左边是变量名，右边是值，只不过这里的值是一函数，它不会提升（hoisting）

Kyle 认为函数表达式全部应该用具名函数，而不是匿名函数，好处有以下：

1. 更可靠的函数自身引用（递归调用自身）
2. 调试 bug 时更好追踪位置
3. 更多的自文档（感觉像注释）的代码，就像 `var handleClick = function submitData() {}`

### Function Types Hierarchy

Kyle 认为函数类型的排序应该是：

1. (Named) Function Declaration
2. Named Function Expression
3. Anonymous Function Expression

## Advanced Scope

### dynamic scope

JavaScript 没有动态 scope

理论上的 dynamic scope 应该是这样的：

```js
var teacher = "Kyle"

function ask(question) {
    console.log(teacher.question)
}
function otherClass() {
    var teacher = "Suzy"
    
    ask("Why?")
}

otherClass()
```

这里面很多东西在 v1 版本里有就没看了

### Hoisting

Hoisting 其实不是真实存在的，而是人们用来理解变量声明，但事实是 JavaScript不会去重构你的代码，而是每次进入一个 scope 就会去找所有的 declaration

函数优先 hoist，再是变量

为什么函数表达式声明不会 hoisting，变量本身会被 hoist，但是表达式后面的 assignment 是运行时的事情，而函数就像一个计划一样，有计划但是没有被执行，

const，let 不会 hoist

说到 let 不会 hoist 就会有想到 temporary dead zone a.k.a TDZ，其实 TDZ 是因为 const 而产生的，变量声明都会 hoisting，一个 const hoisting 后，这个时候它的值会是 undefined，在它的 TDZ 你会得到 undefined，然后你在它被 assign 后你再去访问它的值，这个值可能就不是 undefined 了

在不同的时候访问一个 const 的变量居然完全不一样，这就违背了 const 设计的初衷，所以要有 TDZ，也就是 const 为什么不能 hoist，于是随便把这样的概念给了 let

## Objects

### this

this 既不指向函数本身也不指向函数的词法作用域，this 实际上是在函数被调用时发生的绑定，它的指向完全取决于函数在哪里被调用

### new

1. Create a brand new empty object 创建（或者说构造）一个全新的对象
2. Link that object to another object 这个对象会被执行 [[ 原型 ]] 连接
3. call function with **this** set to the new object 这个对象会被绑定到函数调用的 this
4. If function does not return an object, assume return of this 

如果函数没有返回其它对象，那么 new 表达式中的函数调用会自动返回这个新对象

判断 this：

1. 函数是否在 new 中调用？如果是的话 this 绑定的新创建的对象。`var bar = new foo()`
2. 函数是否通过 call、apply 或者 bind 调用？如果是的话，this 绑定的对象是指定的对象。`var bar = foo.call(obj)`
3. 函数是否在某一个上下文对象中调用？如果是的话，this 绑定的就是那个上下文对象。`var bar = obj.foo()`
4. 如果都不是的话，使用默认绑定。如果在严格默认下，就会绑定到 undefined，否则绑定到全局对象。`var bar = foo()`

箭头函数是没有 this 的，所有它会一直向上查找 this，表现的就像词法作用域一样

> An [ArrowFunction](https://www.ecma-international.org/ecma-262/9.0/index.html#prod-ArrowFunction) does not define local bindings for `arguments`, `super`, `this`, or `new.target`. Any reference to `arguments`, `super`, `this`, or `new.target` within an [ArrowFunction](https://www.ecma-international.org/ecma-262/9.0/index.html#prod-ArrowFunction) must resolve to a binding in a lexically enclosing environment. 

大括号不是区分 scope 的标志，对象不是 scope

Kyle 认为 `var self = this` 这种行为在任何环境下都不应该出现，this 指向的不是函数，而是 context 上下文，哪怕非得这么做也应该用 `var context = this`

在很多用 setTimeout 的时候，可能有会用到原来 this 的情况应该用箭头函数

Kyle 建议只在你需要用词法的 this 的时候才用箭头函数

### ES6 class Keyword

class 里面的方法其实是在 prototype 上的，所以即使在类实例化后其实它的方法是没有绑定到实例上的，你需要手动去绑定，如果你用箭头函数的方式写在类里面，虽然你不用绑定 this 上下文了，但是每个实例都会去重新拥有这个方法，这就可能浪费了大量的内存，即使 JavaScript 会有优化它的方法，但是这是一种很不好的模式。

曾经在写 react 组件的时候就很疑惑，甚至以为是 react 是这样写的，现在知道不是了，class 就是这样的，当时就是用箭头函数去写的，现在才知道那只是一种偷懒且很不好的写法

关于自动绑定应该是在提案中了

## Prototypes

这里的内容就能看到 class 的具体实现

类其实是关于复制操作的，继承也是

A "constructor call" makes an object linked to its own prototype

link 和 copy 是同一东西的两个不同角度去看而已

箭头函数没有原型，普通函数有原型，实例化的对象都会有个 `__proto__` 指向类（函数）的原型

```js
function Workshop(teacher) {
    this.teacher = teacher
}
Workshop.prototype.ask = function(question) {
    console.log(this.teacher, question)
}

function AnotherWorkshop(teacher) {
    Workshop.call(this, teacher)
}
AnotherWorkshop.prototype = Object.create(Workshop.prototype) // Object.create 是 ES5 的，如果不写这个就会指向 Object.prototype，这里有点像是 extends 的实现
AnotherWorkshop.prototype.speakUp = function(msg) {
    this.ask(msg.toUpperCase()) // 会去 Workshop.prototype 里找
}

var JSRecentParts = new AnotherWorkshop("Kyle")

JSRecentParts.speakUp("Is this actually inheritance?")
// "Kyle IS THIS ACTUALLY INHERITANCE?"
```

JavaScript 里面的继承不是真正传统语言意义上的继承，而是连接，它们通过 prototype 连接起来，而且 prototype 根本就不差，而且很独特强大

JavaScript "~~Inheritance~~"  "Behavior Delegation"

Kyle 认为 prototype 系统比 class 系统好的原因，起码 prototype 系统可以实现 class 系统，但是 class 系统不能实现 prototype 系统

要用好 prototype 就要不能只有 class 这种模式的偏见

### OLOO: Objects Linked to Other Objects

#### Object.create

```js
if(!Object.create) {
    Object.create = function (o) {
        function F() {}
        F.prototype = o;
        return new F()
    }
}
```

