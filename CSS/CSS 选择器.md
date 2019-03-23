## 关系选择器
![各种关系选择器](http://ww1.sinaimg.cn/large/b7f2e8afgy1fvpe2azgnkj20yg0guaf9.jpg)
后代选择器选中block中的2个p元素
子元素选择器只选中block的直接子元素p元素(即仅第一个p元素)

## 伪类选择器
![伪类选择器](http://ww1.sinaimg.cn/large/b7f2e8afgy1fvpe30hncfj20yg0huams.jpg)
![伪类选择器](http://ww1.sinaimg.cn/large/b7f2e8afgy1fvpe3jcvq4j20ur0avgpo.jpg)

## 伪元素选择器

![伪元素选择器](http://ww1.sinaimg.cn/large/b7f2e8afgy1fvpe441uwpj20ui0la4ik.jpg)

## 属性选择器
下面我们使用这四个 a 元素来具体实践下：

```
<a href="https://ke.qq.com" target="_blank">腾讯课堂</a>
<a href="css-basic.pdf" >CSS学习文档</a>
<a href="css.png" >CSS 脑图</a>
<a href="http://imweb.io" title=“IMWeb”>IMWeb学院</a>
```

要求如下:
- 选中 title 属性链接
- 选中新窗口打开的链接（可在后面添加一个icon，以区分其他链接）
- 选中不同的文件类型链接（可在后面添加对应的图标，以表示资源类型）
- 选中绝对路径链接

对应的选择器如下：
```
/* 选中 title 属性链接 */
a[title] {}

/* 选中新窗口打开的链接 */
a[target="_blank"] {}

/* 选中 pdf */
a[href$=".pdf"] {}

/* 选中 png */
a[href$=".png"] {}

/* 选中绝对路径链接 */
a[href^="http://"],
a[href^="https://"] {}
```
[属性选择器 | W3School](http://www.w3school.com.cn/css/css_selector_attribute.asp)

## 选择器参考手册

一下讲了这么多选择器，估计一时半会是消化不了的。不过没关系，我们可以慢慢来，视频可以多看几次，也可以先有个印象继续学习后面的内容，然后再回过头来消化消化。同时这里也为你找了一些非常具有参考价值的选择器文档，可以方便你学习查阅。

首先是W3school的选择器参考，归类很详细，非常适合入门学习：

*   [CSS 元素选择器](http://www.w3school.com.cn/css/css_selector_type.asp)
*   [CSS 类选择器详解](http://www.w3school.com.cn/css/css_selector_class.asp)
*   [CSS ID 选择器详解](http://www.w3school.com.cn/css/css_selector_id.asp)
*   [属性选择器详解 | W3School](http://www.w3school.com.cn/css/css_selector_attribute.asp)
*   [CSS 后代选择器](http://www.w3school.com.cn/css/css_selector_descendant.asp)
*   [CSS 子元素选择器](http://www.w3school.com.cn/css/css_selector_child.asp)
*   [CSS 相邻兄弟选择器](http://www.w3school.com.cn/css/css_selector_adjacent_sibling.asp)
*   [CSS 伪类](http://www.w3school.com.cn/css/css_pseudo_classes.asp)
*   [CSS 伪元素](http://www.w3school.com.cn/css/css_pseudo_elements.asp)

或者直接参考选择器手册：

*   [选择器参考手册 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Selectors)（点击相应的选择器为英文链接，可以在地址栏中将 en-US 换成 zh-CN 即可）
*   [CSS 选择器 | 菜鸟教程](http://www.runoob.com/cssref/css-selectors.html)
*   [选择器参考手册 | W3School](http://www.w3school.com.cn/cssref/css_selectors.asp)
*   [Selectors Level 3 | W3](https://www.w3.org/TR/selectors/#selectors)（官方参考手册，英文版）
*   [Selectors Level 4 | W3](https://drafts.csswg.org/selectors-4/)（第四代选择器草稿）

#### 伪类列表

- [`:link`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:link)
- [`:visited`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:visited)
- [`:active`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:active)
- [`:hover`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:hover)
- [`:focus`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:focus)
- [`:first-child`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:first-child)
- [`:nth-child`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-child)
- [`:nth-last-child`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-last-child)
- [`:nth-of-type`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-of-type)
- [`:first-of-type`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:first-of-type)
- [`:last-of-type`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:last-of-type)
- [`:empty`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:empty)
- [`:target`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:target)
- [`:checked`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:checked)
- [`:enabled`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:enabled)
- [`:disabled`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:disabled)

## 选择器效率
根据网站效率专家 [Steve Souders](http://stevesouders.com/) 指出，各种 CSS 选择器的效率由高至低排序如下
- id选择器（#myid）

- 类选择器（.myclassname）

- 标签选择器（div,h1,p）

- 相邻选择器（h1 + p）

- 子选择器（ul > li）

- 后代选择器（li a）

- 通配符选择器（*）

- 属性选择器（a[rel="external"]）

- 伪类选择器（a:hover,li:nth-child）

## 选择器解读顺序
  一般来说，在具体的项目中，HTML 结构都比较复杂，所以关系选择器使用非常的普遍。对于关系选择器来说，我们的阅读习惯是从左到右，但是浏览器解读选择器，遵循的原则是从选择器的右边到左边读取。

如对于选择器.list .item .item-tt，浏览器先找的是.item-tt，然后继续向父级元素寻找.item，再找.list，这样才完成了最终的选择器匹配。

所以如果路径链越短，效率也就相应有所提高。这里建议选择器的层级最多不要超过4层，如.demo .list .item .item-tt .tt-link就有5层了，可根据实际情况考虑缩短为4层以内，如.demo .item-tt .tt-link
### 参考资料

*   [如何提升 CSS 选择器性能](http://www.jianshu.com/p/268c7f3dd7a6)
*   [CSS选择器的优化](https://www.w3cplus.com/css/css-selector-performance)
*   [Efficiently Rendering CSS](https://css-tricks.com/efficiently-rendering-css/)