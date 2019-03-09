## 认识 JavaScript

目前主流浏览器上唯一支持的脚本语言

- ECMAScript 语言基础
- DOM 文档对象模型
- BOM 浏览器对象模型

### 引入方式

1. 行内，最原始，只能写一些时间，

```html
<button id="helloBtn" onclick="alert('hello');">
    点击弹出消息框
</button>
```

2. `<script>`内嵌标签，代码冗余，没有复用性

```html
<script type="text/javascript">
    var helloBtn = document.getElementById('helloBtn');
    helloBtn.onclick = function() {
        alert('hello');
    }
</script>
```

3. 外链引入

```html
<script type="text/javascript" src="hello.js"></script>
```

其中 script 标签的 type 属性并不是必需的，如果没有指定这个属性，则其默认值为 `text/javacript`

### 注释

```js
// 这是单行注释
/*
 这个是多行注释，但是不够美观
*/
/**
* 一段简单的 JSDoc 注释。
*/
```

### 变量

`var 变量名`来申明变量

变量名：

1. 区分大小写，如 age 不等于 AGE
2. 首字符必须是字母、_ 或者$
3. 非关键字和保留字，如 var

```js
var name = 'bottle',
    price = 49;
// 可以一行语句定义多个变量，但是还是推荐一个一个定义
```

- 在一个 function 里面用 var 定义变量就是局部变量
- 在一个 function 里面不用 var 定义变量就是全局变量

### 数据类型

基本数据类型：

- Number
- String
- Null
- Boolean
- Undefined

引用类型：Object

typeof 判断数据类型，不是函数，是操作符

typeof 会将 null 和数组都认成 object

o instanceof C 相当于 Object.getPrototypeOf(o) === C.prototype 的结果

### String

```js
var type = '多彩保温杯茶杯'
// 直接访问字符串索引可以会有老浏览器的兼容性问题，charAt() 就不会有
console.log(type[0]) // 多
console.log(type[2]) // 保
console.log(type.charAt(1)) // 彩
```

length 返回字符串的长度

### Boolean

! 操作符，可以得到很多隐性的 boolean 类型

```js
!!1 // true
!1 // false

!!-3.14 // true

!!0 // false
!!NaN // false
!!'' // false
!!null // false
!!undefined // false

!!' ' // true
!!'0' // true
!!'false' // true
!![] // true
!!{} // true
```

### Null 和 Undefined

| 数据类型 | Null | Undefined |
| :------: | :--: | :-------: |
|   取值   | null | undefined |
| 代表含义 | 数据为空 | 变量声明了，但是没有初始值 |
| 例子 | var a = null; | var a; |
| typeof 结果 | object | undefined |
| 对应的 Boolean 值 | false | false |

- 初始化变量是如果不赋值，变量的值是 `undefined`, 不是 `null`。
- `typeof null` 的值是 `object`。
- `!!null` 的值是 `false`。
- `Null` 类型只有一个值 `null`, `Undefined` 也只有一个值 `undefined`。

### Object 类型

```JS
// 对象字面量创建对象，推荐写法
var bottle = {
    name: 'bottle',
    price: 49,
    'is keep warm': true
}
console.log(bottle.name) // 'bottle',推荐这个种写法
console.log(bootle['name']) // 'bottle'
console.log(bottle['is keep warm']) // true
console.log(bottle.'is keep warm') // 报错
// 写操作直接写
bottle.color = 'blue'

```

### 数组

```js
var bottles = ['绿', '蓝', '紫', '红'] // 数组字面量，推荐写法
var bottles = new Array('绿', '蓝', '紫', '红') // Array 构造函数
```

#### 简单方法

push：在数组末尾添加一个元素

pop：将数组最后一个元素去除掉

shift：去掉第一个元素，并改变索引

unshift：在数组开头添加一个元素

### 函数

function 关键字，函数名可以是数字字母下划线，但是开头不能是数字

```js
function count(element) {
    // 函数声明
}
var count = function(element) {
    // 函数表达式
}
```

JavaScript 引擎将函数名视同变量名，所以采用`function`命令声明函数时，整个函数会像变量声明一样，被提升到代码头部。所以，下面的代码不会报错。

```
f();
function f() {}
```

### 作用域

全局作用域和函数作用域

全局变量和局部变量

### 算数运算符

```js
var a = 1
console.log(--a) // 0, 先自减，再运算整个表达式
a = 1
console.log(a--) // 1,先运算整个表达式，再自减
```

### 赋值运算符

```js
a = a + 1 // 等同于 a += 1
```

### 比较运算符

!= 不相等  !== 严格不相等

```js
3 == '3' // true
3 === '3' // false
// 相等是先类型转换，再比较
// 除了严格相等不会进行类型转换，其它都会进行类型转换
```

### 逻辑运算符

- ! (逻辑非)
- && (逻辑与)
- || (逻辑或)