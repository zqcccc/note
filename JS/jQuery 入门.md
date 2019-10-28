### 引入 jQuery

jQuery 相当于一个库，也是一个 js 文件，和普通的 js 引入没什么区别

```html
<!-- 本地文件引入 -->
<script src="js/jquery.min.js"></script>
<!-- 内容分布式网络 CDN -->
<!-- 非压缩版 开发用 -->
<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.js"></script>
<!-- 压缩版 正式发布用 -->
<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
```

### jQuery 中的 $

jQuery 中的 $ 其实就是 jquery 的别称

$() => jQuery 对象，类数组带有额外方法

```js
$ === jQuery // true
// $(string)
$('.footer') // 将类名为 footer 的元素转换为 jquery 对象
$('<div></div>') // 生成一个 div 的 jquery 对象
// $(dom element)
var paragraphs = document.getElementByTagName('p')
var $paragraphs = $(paragraphs) // 将 DOM 转换为 jQuery 对象
// 使用eq、get 索引
var $firstParagraph = $paragraphs.eq(0) // jQuery 对象
var firstParagraph = $paragraphs[0] // DOM
var secondParagraph = $paragraphs.get(1) // DOM
```

### 选择器

jQuery 选择器实现了 CSS 选择器，基本选择器、组合选择器、其他选择器。

- -$(selector1, selector2) 多元素选择器

- $(selector1  selector2) 后代选择器

- $(selector1 > selector2) 子元素选择器

- $(selector1 + selector2) 毗邻选择器

- $(selector1:first-child) 选取第一个子元素
- $(selector1:last-child) 选取最后一个子元素
- $(selector1:eq(index)) 选取集合中第 index 个元素
- $(attitude = value) 选取属性为 value 的元素 

jquery 的选择器不只这些，但是常用的就是这些

```js
$('li:last-child') // 多个 ul 的话，会选中多个 ul 中的最后一个 li
```

### 遍历 DOM 元素

```js
$('#me').parent() // 获取父元素
$('#me').parents() // 获取所有的父元素和祖先元素

// 使用 .eq() 函数获取相应元素
$('#me').parents().eq(0) // 获取爸爸
$('#me').parents().eq(1) // 获取爷爷

// 使用 .parents(selector) 来精准查找
$('#me').parents('.fat')
$('#me').parents('.oldest')

$('#me').siblings() // 获取所有的兄弟元素
$('#me').sibling('.tall') // 通过使用 selector 来精确查找

$('#grandpa').children() // 使用 .children() 获取子元素
$('#grandpa').find(*) // 使用 .find(*) 获取所有子孙元素
$('#grandpa').find('.youngest') // 通过 selector 也可以精准查询
```

### 添加和移除 DOM 元素

#### 添加

- append / appendTo 在被选元素的结尾插入内容
- prepend / prependTo 在被选元素的开头插入内容
- after / insertAfter 在被选元素之后插入内容
- before / insertBefore 在被选元素之前插入内容

#### 删除

- empty 移除所有的子元素
- remove 连同自己都移除了

```js
$(document.body).append('<div>哈哈1</div>')
$('<div>哈哈1</div>').appendTo(document.body)
$('#father').empty() // 移除所有的子元素
$('#father').remove() // 连同自己都移除了
```

### jQuery 事件

- 触发事件的目标元素

- 触发的事件名称

- 事件触发时的回调
- 事件对象

jQuery 事件对象封装了原生事件对象，做了浏览器兼容，还添加了好用的属性和方法

```js
$('a').on('click', function(event) {
    console.log(event.target) // 获取触发事件的元素
    /*event.preventDefault() // 阻止默认事件
    event.stopPropagation() // 阻止事件冒泡 */
    return false // 既阻止默认事件，且阻止事件冒泡
})
$('#target').click(function(event) { // 使用快捷函数 click，此外还有 focus(),blur(),keydown() 等，但是还是只推荐用 on(),因为事件委托用 on() 更方便
    console.log('target')
})
$('#button').on('click', function(event) {
    $('#target').css('backgroundColor', 'yellow')
})
```

