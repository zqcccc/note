# Web 相关编码和转义

提到 Web 前端安全，第一个想到的就是 XSS。
而提到 XSS，不得不说的就是编码了。事实上，编码涉及的知识十分的复杂，很难短时间梳理清楚和弄明白。在这里只要求大家对编码有一个概念就好，日后再进行深究。

## 常用编码

本文从 XSS 中常用的编码讲起，分为

- **URL 编码**
- **HTML 编码**
- **JS 编码**

## URL编码

一般来说，URL只能使用英文字母（`a-zA-Z`）、数字（`0-9`）、`-_.~`4个特殊字符以及所有（`;,/?:@&=+$#`）保留字符。

意味着如果使用了一些其他文字和特殊字符，则需要通过编码的方式来进行表示，如：

```js
// 使用了汉字
var url1 = 'http://www.帅.com';
```

另外我们知道在 URL 中传参是通过**键值对**形式进行的，格式上是以`？`、`&` 和 `=` 为特征标识进行解析，如果在键或者值的内容中包含一些特殊符号，就会造成解析错误，如下所示：

```js
// 键为汉字
var url2 = 'http://www.a.com?我=1';
// 值的内容为特殊符号
var url3 = 'http://a.com?key=?&';
```

对于上面的情况如果我们要正常解析，则需要进行编码，需要用不会造成歧义的符号代替有歧义的符号。

我们可以通过使用系统原生实现的 API 来对字符进行 **URL 编码**如:

- [encodeURI()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURI)
- [encodeURIComponent()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)
- [escape 逐渐废弃可以不理会](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/escape)

### encodeURI

**encodeURI** 是用来编码 URI 的，最常见的就是编码一个 URL。**encodeURI** 会将需要编码的字符转换为 `UTF-8` 的格式。对于保留字符（`;,/?:@&=+$#`），以及非转义字符（字母数字以及 `-_.!~*'()`）不会进行转义。

例如之前 URL 中包含中文，我们可以使用 encodeURI：

```js
encodeURI('http://www.帅.com'); // http://www.%E5%B8%85.com
encodeURI('http://www.a.com?我=1');// "http://www.a.com?%E6%88%91=1"
```

在这里，`%E5%B8%85` 就是 `帅` 的 URL 编码，`%E6%88%91` 即为 `我` 的 URL 编码。然后由于 **encodeURI** 不转义`&`、`?`和`=`。所以对于 URL 参数的值是无法转义的，如下面的例子：

```js
// 值的内容为特殊符号
encodeURI('http://a.com?key=?&'); // "http://a.com?key=?&"
```

此时我们就需要使用 **encodeURIComponent** 来解决。

### encodeURIComponent

顾名思义，**encodeURIComponent** 是用来编码 URI 参数的。它会跳过非转义字符（字母数字以及`-_.!~*'()`）。但会转义 URL的 保留字符（`;,/?:@&=+$#`）。通常来说我们会 **encodeURI** 结合 **encodeURIComponent** 来使用，如下所示：

```js
// "http://a.com?a=%3F%26"
encodeURI('http://a.com') + '?a=' + encodeURIComponent('?&');  
```

其中 `%3F` 和 `%26` 分别为 `?` 和 `&` 的 URL 编码。需注意的是由于 **encodeURIComponent** 会编码所有的 URL 保留字，所以不适合编码 URL，例如：

```js
// "http%3A%2F%2Fa.com%3Fkey%3D%3F%26"
encodeURIComponent('http://a.com?key=?&');
```

上面的 `http%3A%2F%2Fa.com%3Fkey%3D%3F%26` 在地址栏会被解析为一个普通的字符串而不是 URL。

## URL 解码

有了 URL 编码，相应的会有解码机制。比如上面对应的 3个 encode 的API对应的解码 API 如下：

- [decodeURI()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/decodeURI)
- [decodeURIComponent()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/decodeURIComponent)

## HTML 编码

在 HTML 中，某些字符是预留的，比如不能使用小于号（`<`）和大于号（`>`），这是因为浏览器会误认为它们是标签。如果希望正确地显示预留字符，我们必须在 HTML 源代码中使用字符实体（character entities）。当然还另一个重要原因，有些字符在 ASCII 字符集中没有定义，因此需要使用字符实体来表示，比如中文。

HTML 编码分为：

- HTML 十六进制编码 `&#xH;`
- HTML 十进制编码 `&#D;`
- HTML 实体编码 `&lt;` 等

在 HTML 进制编码中其中的数字则是对应字符的 unicode 字符编码。
比如单引号的 unicode 字符编码是27，则单引号可以被编码为`&#x27;`

### HTML 实体编码

通常来说，在业务中我们用到更多的是 HTML 的实体编码。常用的 HTML 实体编码函数如下所示：

```js
/**
 * 转义 HTML 特殊字符
 * @param {String} str
 */
function htmlEncode(str) {
  return String(str)
    .replace(/&/g, '&amp;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#39;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;');
}
```

更完整的转义列表见[这里](http://w3school.com.cn/html/html_entities.asp)。

## Javascript 转义

JavaScript 中有些字符有特殊用途，如果字符串中想使用这些字符原来的含义，需要使用反斜杠对这些特殊符号进行[转义](http://www.w3school.com.cn/js/js_special_characters.asp)。我们称之为 **Javascript编码**。一般有以下几类：

- 三个八进制数字，如果不够个数，前面补0，例如 “e” 编码为“\145”
- 两个十六进制数字，如果不够个数，前面补0，例如 “e” 编码为“\x65”
- 四个十六进制数字，如果不够个数，前面补0，例如 “e” 编码为“\u0065”
- 对于一些控制字符，使用特殊的C类型的转义风格（例如\n和\r）

如下面所示，双引号用于标注字符串，然而在字符串中带了双引号，就会发生歧义：

```js
var str = "Hello"";
```

于是我们需要对字符串内的双引号进行转义，也就是加上反斜杠，告诉脚本引擎要区分对待：

```js
var str = "Hello\"";
```

## 更多阅读

对 Web 相关编码和转义十分感兴趣的同学可以阅读下面的文章和内容来加深理解。

- [前端的各种转义](https://github.com/FrankFang/githublog/blob/master/%E6%8A%80%E6%9C%AF/%E5%89%8D%E7%AB%AF%E7%9A%84%E5%90%84%E7%A7%8D%E8%BD%AC%E4%B9%89.md)
- [从零开始学web安全（3）](http://imweb.io/topic/57024e4606f2400432c1396d)