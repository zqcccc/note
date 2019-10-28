## 元素
![HTML 元素](https://ws1.sinaimg.cn/large/b7f2e8afgy1fvpextj5kej20dy0aqwfi.jpg)
## 注释
当我写下这段代码的时候，只有我和上帝能看懂。现在，只有上帝能了
```html
<!-- 这里面就可以写注释了 -->
```
为了表示元素的一些特征，我们可以在开始标签中添加一些属性。如下：

![](https://ws1.sinaimg.cn/large/b7f2e8afgy1fvpey4753lj20qq066754.jpg)

## 属性特征

一般属性都具有以下特征：

*   每个属性之间或与元素名之间都有一个空格隔开
*   属性名后面紧跟等号
*   属性值使用双引号包裹

但也有一些属性只有属性名，没有属性值，这种属性我们称之为布尔属性，如下`disabled`就是布尔属性，表示是否禁用，有该属性则为禁用：

```
<input type="text" disabled>

```

除此之外，我们还可以自定义属性来存储我们的一些数据以便 JS 使用。具体可参考：[HTML data-* 属性](http://www.w3school.com.cn/tags/att_global_data.asp)。（我们后面的课程会讲解，先不用纠结）

## 属性分类

属性有很多种，从使用来说大概可以分为三类：

*   可以用于每个元素的全局属性，如`class`属性
*   可用于某一类元素的，如`form`表单相关元素的`name`、`value`属性
*   只用于某一个元素的，如`alt`属性只用于`img`元素

这里我们先介绍几个常用的全局属性，其他得两种属性我们在稍后的相关课程将会说明。

### class

用来设置元素的一个或多个类名，这样以后的 CSS 或 JS 就可以方便对该元素进行操作。具有以下特点：

*   类名不能以数字开头（一定要牢记）
*   类名可以设置多个值，以空格分开，如`<div class="box box--menu"></div>`
*   不同的元素可以有相同的类名

简单示例如下：

```
<p class="p1 red">文本段落</p>
<div class="red">我也有一个类名为red</div>

```

### id

设置元素的唯一性，经常用于 JS 操作或 CSS 操作，也可用作定义锚点。具有以下特点：

*   在整个 HTML 文档中必须是唯一的，也就是说一个 HTML 文档中不能出现两个一样的id值
*   不可以和 class 那样设置多个值

简单示例如下：

```
<p id="text">文本段落</p>
<div id="block">区块内容</div>

```

### title

用来设置元素的额外信息，鼠标滑过元素暂停一会会显示 title 属性的内容。大概效果如下图：

![img](http://p.qpic.cn/qqconadmin/0/d1390d8cef4b40a68dabd9e757d179ca/0)

简单示例如下：

```
<a title="全部的链接文字" href="#">链接文字很多...</a>

```

### style

用于设置元素的行内样式，一般用于 JS 动态改变元素的样式。(以后讲 CSS 的时候会详细介绍)

简单示例如下：

```
<div style="display: none;">我是隐藏的区域，可以通过JS来切换显示</div>
<p style="width: 500px;">设置宽度为500px</p>

```

### 更多全局属性参考

*   [HTML 全局属性 | W3school](http://www.w3school.com.cn/tags/html_ref_standardattributes.asp)
*   [HTML 全局属性 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes)

另附 HTML 属性参考手册一份：[HTML 属性参考](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes)。