# Date 类型

在实际应用中，我们常常需要用到日期或者时间等，而在 JS 中，我们通常使用 Date 类型来表示日期。

由于 JS 中的 Date 类型是在早期 Java 中的 java.util.Date 类基础上构建的，所以 JS 的 Date 类型使用的是自 UTC 1970年1月1日0时0分0秒（世界标准时间）起的毫秒数。比如2017年8月7号16时37分26秒, 用毫秒数表示就是 1502095046000。当然，我们实际使用中不需要手动去计算这些毫秒数，但是要理解这个数字的含义。

## 日期对象

通常我们调用 `new Date()` 获取当前时间，函数返回的是日期对象，这个对象有很多方法可以获取日期的信息。

```js
// 当前时间
var now = new Date(); // Tue Aug 08 2017 15:41:26 GMT+0800 (CST)
```

除此之外，我们还可以传入参数使用，如：

```js
var today = new Date(1453094034000); // by timestamp(accurate to the millimeter)
var birthday = new Date('December 17, 1995 03:24:00');
var birthday = new Date('1995-12-17T03:24:00');
var birthday = new Date(1995, 11, 17);
var birthday = new Date(1995, 11, 17, 3, 24, 0);
```

其参数规则如下：

```js
// value 代表自1970年1月1日00:00:00 (世界标准时间) 起经过的毫秒数
new Date(value);

// 表示日期的字符串值
new Date(dateString);

// year 代表年份的整数值，为了避免2000年问题最好指定4位数的年份; 如使用 1998, 而不要用 98
// month 代表月份的整数值从0（1月）到11（12月）
// day 代表一个月中的第几天的整数值，从1开始
// hour 代表一天中的小时数的整数值 (24小时制)，minute 分钟数，second 秒数，millisecond 毫秒数
new Date(year, month[, day[, hour[, minutes[, seconds[, milliseconds]]]]]);
```

注：由于日期对象的方法实在有点多，这里就抽取几个常用的说明下，其余的可参考我们下面的参考资料文章。

## 静态方法

### now 方法

es5 中添加了 `Date.now()` 方法，返回表示调用这个方法时的日期和时间的毫秒数。我们经常用此方法来测试代码的运行时间，这样就可以根据运行时间判断其性能了，如：

```js
// 记录开始时间
var start = Date.now();

// 这里运行一段代码
doSomething();

// 记录结束时间
var stop = Date.now();

// 得到代码运行时间
var timeUse = start - stop;
```

但是对于不支持 `Date.now()` 方法的浏览器（如 IE8-），我们还可以使用+操作符把 Date 对象转换成字符串，也可以达到同样的目的，如下：

```js
// 记录开始时间
var start = +new Date();

// 这里运行一段代码
doSomething();

// 记录结束时间
var stop = +new Date();

// 得到代码运行时间
var timeUse = start - stop;
```

## 实例方法

### Getter

- `getFullYear()`：根据本地时间返回指定日期对象的年份（四位数年份时返回四位数字）
- `getMonth()`：根据本地时间返回指定日期对象的月份（0-11）
- `getDate()`：根据本地时间返回指定日期对象的月份中的第几天（1-31）
- `getDay()`：根据本地时间返回指定日期对象的星期中的第几天（0-6）
- `getHours()`：根据本地时间返回指定日期对象的小时（0-23）
- `getMinutes()`：根据本地时间返回指定日期对象的分钟（0-59）
- `getSeconds()`：根据本地时间返回指定日期对象的秒数（0-59）
- `getMilliseconds()`：根据本地时间返回指定日期对象的微秒（0-999）
- `getTime()`：返回从1970-1-1 00:00:00 UTC（协调世界时）到该日期经过的毫秒数，对于1970-1-1 00:00:00 UTC之前的时间返回负值。

下面我们以一个简单的格式化时间来说明下上面方法的一些使用：

```js
// 将时间格式化为：YYYY-MM-DD HH:mm:ss

// 小于或等于9的数字前面添加0
function addZero(num) {
    return num > 9 ? num : '0' + num;
}

// 格式化时间，可传入一个时间或使用当前时间
function dateFormat(date) {
    var date = date ? new Date(date) : new Date();
    var str = date.getFullYear() + '-' + addZero(date.getMonth() + 1) + '-' + addZero(date.getDate()) + ' ' + addZero(date.getHours()) + ':' + addZero(date.getMinutes()) + ':' + addZero(date.getSeconds());

    return str;
}

dateFormat(); // 2017-08-07 17:44:51
dateFormat(1502095046000); // 2017-08-07 16:37:26
dateFormat('December 3, 1995 03:19:05'); // 1995-12-03 03:19:05
```

当然对于格式化时间这么常用的功能，已经有[Moment.js](http://momentjs.cn/)库来帮我们处理了。

### Setter

- `setFullYear()`：根据本地时间为指定日期对象设置完整年份（四位数年份是四个数字）
- `setMonth()`：根据本地时间为指定日期对象设置月份
- `setDate()`：根据本地时间为指定的日期对象设置月份中的第几天
- `setHours()`：根据本地时间为指定日期对象设置小时数
- `setMinutes()`：根据本地时间为指定日期对象设置分钟数
- `setSeconds()`：根据本地时间为指定日期对象设置秒数
- `setMilliseconds()`：根据本地时间为指定日期对象设置毫秒数
- `setTime()`：通过指定从 1970-1-1 00:00:00 UTC 开始经过的毫秒数来设置日期对象的时间，对于早于 1970-1-1 00:00:00 UTC的时间可使用负值。

对于该系列的方法，我们经常用于倒计时，如下：

```js
// 2017 年 IMWebconf 大会开始时间为： 2017-09-16 09:30:00
// 计算大会时间的倒计时

var nowTime = Date.now(); // 1502189810994，这个时间会变动

var confDate = new Date(2017, 8, 16, 9, 30, 0),
    confTime = confDate.getTime(); // 1505525400000

var diffTime = parseInt((confTime - nowTime) / 1000);

if(diffTime > 0) {
    var days = parseInt(diffTime / (60 * 60 * 24)); // 得到剩余天数
    var hours = parseInt((diffTime % (60 * 60 * 24)) / (60 * 60)); // 得到剩余小时
    var minutes = parseInt(((diffTime % (60 * 60 * 24)) % (60 * 60)) / 60); // 得到剩余分钟
    var seconds = ((diffTime % (60 * 60 * 24)) % (60 * 60)) % 60; // 得到剩余秒
    // 距离大会还剩38天14:33:9，依赖于 nowTime 为 1502189810994 的计算
    console.log('距离大会还剩' + days + '天' + hours + ':' + minutes + ':' + seconds);
} else {
    console.log('大会已经圆满结束，敬请期待下一次大会！')
}
```

同样对于倒计时，也有很多库可用：[分享12个效果精美的 JavaScript 倒计时脚本](http://www.cnblogs.com/lhb25/archive/2012/12/05/12-essential-countdown-timer-scripts.html)

## 参考资料

- [Date | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)
- [Date对象](http://javascript.ruanyifeng.com/stdlib/date.html)