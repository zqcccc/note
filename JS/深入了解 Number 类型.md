# 深入了解 Number 类型

Number 类型作为 JS 的基本数据类型之一，被应用在程序中的各种场景，其重要性就如数字对于我们日常生活。

下面就让我们来一起深入了解下，为以后的“策马奔腾”做好铺垫。

## 定义方式

一般来说我们可以直接使用**数值字面量**格式来定义一个数字，如下：

```js
var num1 = 15;
var num2 = 7;

console.log(typeof num1); // number
console.log(typeof num2); // number
```

## 数值类型

定义的数值可分为两种类型，分别为整数和浮点数。

### 整数

整数，可以通过十进制，八进制，十六进制的字面值来表示。（默认为十进值）

```js
// 十进制
var intNum1 = 55; // 正数
var intNum2 = 0; // 0
var intNum3 = -3; // 负数

// 八进制
// 第一位必须是0，其余位的取值范围为0-7
// 无效的八进制会直接忽略前面的0，解析为十进制
var octalNum1 = 070; // 八进制的56（7*8 + 0）
var octalNum2 = 079; // 无效的八进制数，9超过了8进制数的范围，解析为79
var octalNum3 = 08; // 无效的八进制数，直接解析为8

// 十六进制
// 前两位必须是0x，其余位的取值范围为 0~9 或 A~F
var hexNum1 = 0xA; // 十六进制的10
var hexNum2 = 0x1f; // 十六进制的31（1*16 + 15）
```

在进行算数计算时，所有以八进制和十六进制表示的数值最终都将被转换成十进制的数值。

```js
// 对前面定义的八进制和十六进制数值进行运算
console.log(octalNum1 + hexNum1); // 66
```

### 浮点数

浮点数其实就是我们通常所说的小数，所以一定有个小数点。简单示例如下：

```js
var floatNum1 = 5.2;
var floatNum2 = 3.14;
```

浮点数所占据的内存空间是整数的两倍。如果小数点后只有零或没有数字，为了节省内存空间，则该小数会被转化为整数，如下：

```js
var floatNum3 = 5.0; // 5
var floatNum4 = 2.; // 2
```

进行算术运算时，浮点数不如整数精准，所以一般不要使用浮点数进行计算，如下：

```js
var floatNum4 = 0.1; 
var floatNum5 = 0.2; 

// 0.1 + 0.2 不等于 0.3
console.log(floatNum4 + floatNum5); // 0.30000000000000004
```

对极大极小的浮点数一般会采用e表示法，如下：

```js
 var floatNum6 = 3.2e7；// 3.2×10（7次幂）
 var floatNum7 = 3.2e-7；// 3.2×10（-7次幂）
```

## NaN

NaN 是 not a number 的简写，即非数字。它是一个特殊的值，这个数值用于表示一个本来要返回数值的操作数，结果未返回数值的情况。

NaN 有两个不同寻常的特点：

- 任何涉及 NaN 的操作都会返回 NaN
- NaN 值与任何值都不相等，包括本身。

```js
console.log(NaN / 10); // NaN
console.log(NaN == NaN); // false
```

针对这两个特点，JS 提供了一个 `isNaN()`函数。该函数接受一个参数（可以是任何类型），而函数会帮我们确定这个参数是否“不是数值”。

注：传递的参数会涉及数值转换的问题，例如“10”这个字符串就可以转换为 10，但是“blue”这个字符串则无法转换为数字，所以 `isNaN('blue') == true`

```js
console.log(isNaN(NaN)); // true
console.log(isNaN(10)); // false
console.log(isNaN("10")); // false，可以被转成数值 10
console.log(isNaN("blue")); // true
console.log(isNaN(true)); // false，可以被转成数值 1
```

## 数值转换

有三个函数可以把非数值转换为数值：`Number()`，`parseInt()`，`parseFloat()`。第一个可以用于任何数据类型，后两个则专门用于把字符串转化为数值。

简单示例如下：

```js
// Number()
// 转换规则比较复杂，可详细参考下面的资料
var numN1 = Number("Hello world!");  // NaN
var numN2 = Number(" ");  // 0 空字符串转为0
var numN3 = Number("000011");  // 11
var numN4 = Number(true);  // 1

// parseInt
// 忽略小数点
// 字符串会被转成数值
var numI1 = parseInt(22.5);   // 22
var numI2 = parseInt ("1234blue") ;  // 1234
var numI3 = parseInt (" ") ;   // NaN
var numI4 = parseInt("70");  //70（十进制数）
var numI5 = parseInt ("070") ;  // 56（八进制数）
var numI6 = parseInt ("0xA") ;  // 10（十六进制数）

// parseFloat
// 字符串会被转成数值
// 如果有多个小数点，则只取第一个，其余全部舍弃
var numF1 = parseFloat ("1234blue") ;  // 1234（整数）
var numF2 = parseFloat("0xA");   // 0
var numF3 = parseFloat("22.5");  // 22.5
var numF4 = parseFloat("22.34.5");  // 22.34
var numF5 = parseFloat("0908.5");   // 908.5
var numF6 = parseFloat("3.125e7");   // 31250000
```

详细介绍可参考：

- [JavaScript数值转换(非数值转换为数值)](http://www.itxueyuan.org/view/6703.html)
- [parseInt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)
- [parseFloat](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseFloat)

## 数值范围

由于内存的限制，JS 并不能保存所有的数值。那么其能表示的最大最小值到底是多少呢？我们可以使用 Number 对象的 `MIN_VALUE` 和 `MAX_VALUE` 属性表示（很少很少用到，大概知道就可以，真要用的时候可以再查阅）：

- `Number.MIN_VALUE` 为能表示的最小正数即最接近 0 的正数 (实际上不会变成 0)，它的近似值为 5e-324
- `Number.MAX_VALUE` 为能表示的最大正数，它的近似值为 1.7976931348623157e+308

如果一个数值超过了最大能表示数值，则自动变成 Infinity 值（无穷数），我们可以使用 Number 对象的 `isFinite()` 来判断一个数是否是有限数，如果非有限数则为无穷数。

```js
console.log(Number.isFinite(56)); // true
console.log(Number.isFinite(Number.MAX_VALUE + Number.MAX_VALUE)); // false
```

更多 Number 对象的属性和方法可参考：[Number 对象 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)

## 数学函数

回忆想想，我们上学的时候是不是学过很多处理数字的数学公式啊，那在 JS 中该怎么办？

不用慌，JS 中内置了一个 Math 对象，它具有数学常数和函数的属性和方法。

我们先来几个简单的例子：

```js
// 函数返回一个数字四舍五入后最接近的整数值。
Math.round(3.4); // 3

// 函数返回一个随机浮点数, 范围在[0，1)
Math.random(); // 随机浮点数，每次都不一样

// 函数返回一个数的平方根
Math.sqrt(9); // 3

// 函数返回给定的一组数字中的最大值
Math.max(10, 20, 13, 18);   //  20

//sin 方法返回一个 -1 到 1 之间的数值，表示给定角度（单位：弧度）的正弦值。
// Math.PI 表示圆周率，一个圆的周长和直径之比，约等于 3.14159.
Math.sin(Math.PI / 2); // 1
```

更多 Math 对象可参考：[Math 对象 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math)