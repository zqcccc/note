# 面向未来的 ES6 模块标准

既然模块化已经越来越重要，那么从语言层面上直接去解决这个问题就显得很有必要（况且其他语言早就有了）。于是 ES6 直接在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

## 设计思想

简单来说，ES6 模块的设计思想就是：一个 JS 文件就代表一个 JS 模块。在模块中你可以使用 import 和 export 关键字来导入或导出模块中的东西。

ES6 模块主要具备以下几个基本特点：

- 自动开启严格模式，即使你没有写 `use strict`
- 每个模块都有自己的上下文，每一个模块内声明的变量都是局部变量，不会污染全局作用域
- 模块中可以导入和导出各种类型的变量，如函数，对象，字符串，数字，布尔值，类等
- 每一个模块只加载一次，每一个 JS 只执行一次， 如果下次再去加载同目录下同文件，直接从内存中读取。 一个模块就是一个单例，或者说就是一个对象

## export

export 命令用于规定模块的对外接口。如果你希望外部能够读取模块内部的变量，函数或类等，就必须使用 export 关键字输出该内容。

下面我们以个人所得税计算为一个模块，来具体使用下 export：

```js
// 个人所得税计算模块
// 在线参考站点：[个人所得税](http://www.gerensuodeshui.cn/)
// personal-income-tax.js

// 个税起征点
export const taxBasicNum = 3500;

// 税率等级
export const taxRatioLevel = [
    {
        num: 1500, // 小于1500
        ratio: '3%',
        subtract: 0, // 速算扣除数
    }, 
    {
        num: 4500, // 大于1500，小于4500
        ratio: '10%',
        subtract: 105,
    }, 
    {
        num: 9000, // 大于4500，小于9000
        ratio: '20%',
        subtract: 555,
    }, 
    {
        num: 35000, // 大于9000，小于35000
        ratio: '25%',
        subtract: 1005,
    }, 
    {
        num: 55000,  // 大于35000，小于55000
        ratio: '30%',
        subtract: 2755,
    }, 
    {
        num: 80000,  // 大于55000，小于80000
        ratio: '35%',
        subtract: 5505,
    }, 
    {
        num: 80000,  // 大于80000
        ratio: '45%',
        subtract: 13505,
    }];

// 所缴税收
// 应纳税所得额 = 应发工资 － 五险一金 － 个税起征点
// 所缴税收 = 应纳税所得额 * 税率 - 速算扣除数
export function calTax (num, insurance) {
    let taxShouldNum = num - insurance - taxBasicNum;
    let tax;
    switch (true) {
        case taxShouldNum < taxRatioLevel[0].num:
            tax = taxShouldNum * taxRatioLevel[0].ratio - taxRatioLevel[0].subtract;
            break;
        case taxShouldNum < taxRatioLevel[1].num:
            tax = taxShouldNum * taxRatioLevel[1].ratio - taxRatioLevel[1].subtract;
            break;
        case taxShouldNum < taxRatioLevel[2].num:
            tax = taxShouldNum * taxRatioLevel[2].ratio - taxRatioLevel[2].subtract;
            break;
        case taxShouldNum < taxRatioLevel[3].num:
            tax = taxShouldNum * taxRatioLevel[3].ratio - taxRatioLevel[3].subtract;
            break;
        case taxShouldNum < taxRatioLevel[4].num:
            tax = taxShouldNum * taxRatioLevel[4].ratio - taxRatioLevel[4].subtract;
            break;
        case taxShouldNum < taxRatioLevel[5].num:
            tax = taxShouldNum * taxRatioLevel[5].ratio - taxRatioLevel[5].subtract;
            break;
        case taxShouldNum > taxRatioLevel[6].num:
            tax = taxShouldNum * taxRatioLevel[6].ratio - taxRatioLevel[6].subtract;
            break;
        default:
            tax = 0;
    }

    return tax;
}

// 实发工资
export function calWages(num, insurance) {
    let tax = calTax(num, insurance);
    let wages = num - insurance - tax;

    return wages;
}
```

## import

使用 export 命令定义了模块的对外接口以后，其他 JS 文件就可以通过 import 命令加载这个模块。现在我们导入前面设计的个人所得税计算模块。

```js
// main.js
import {taxBasicNum, taxRatioLevel, calTax, calWages} from './personal-income-tax';

// 可以使用 taxBasicNum 输出一段话，说明个税的起征点是多少
console.log(`个税起征点为：taxBasicNum`);
// 还可以使用 taxRatioLevel 数据输出一个表格，对应各个等级的税率，这里就不演示了

// 计算20000元缴纳了五险一金3000后，应该缴纳多少税收及实际税后工资为多少
let tax = calTax(20000, 3000);
let wages = calWages(20000, 3000);
```

## 更多 ES6 模块知识

通过上面的 export 和 import 的使用，是不是觉得 ES6 的模块很方便，也很简单。当然上面都是些基础的运用，而关于 ES6 模块网上也已经有了很多详细的文档，我们在这就不一一介绍了，这里推荐两篇详细文档以供参考学习：

- [[译\]JavaScript ES6模块指南](https://segmentfault.com/a/1190000004100661)
- [ECMAScript 6 入门之 Module 的语法](http://es6.ruanyifeng.com/#docs/module)

注：目前现代浏览器对 ES6 模块支持程度不同，所以一般都是使用 [babel](https://babeljs.io/) 把 ES6 代码转化为兼容的 ES5 版本的代码。