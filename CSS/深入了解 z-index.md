# 深入了解 z-index

网页正常文档流排版可以理解为在一个平面立面里面，元素占据空间，依次排列，互不覆盖。但是当页面中元素设置了定位属性的时候，难免会出现元素之间相互重叠的情况，比如下图小猫和小狗的图片都设置了绝对定位，2张图片的位置重叠了，小猫图显示在小狗图上面。现在如果我们想要小狗图显示在上面，就需要涉及到 CSS 中的 z-index 属性了。

![z-index](http://coding.imweb.io/img/p3/z-index/z-index-1.png)

## z-index

z-index 属性用于指定已定位元素在垂直于页面方向的排列顺序，其属性值有2种：`auto`（默认值）和整数。这里有2个需要注意的点：

1. z-index 属性只对定位元素元素生效，也就是 position 属性不为 static 的元素。
2. 除了默认值 `auto`， z-index 可以设置为任意整数，正数，0，负数都可以。

一般情况下，z-index 值进行比较有下面2条规则：

1. 数值大的在上面(`auto` 数值上相当于0)。
2. 数值相同的，在 HTML 结构中排后面的在上面。

所以上面例子，2张图都没设置 z-index 的值，在 dom 结构上排后面的小猫图展示在上面。如果想小狗图展示在小猫图上面，只需要设置小狗图的 z-index 属性值大于0就可以了。

```html
<img class="dog" src="http://coding.imweb.io/img/p3/z-index/dog.png" alt="dog">
<img class="cat" src="http://coding.imweb.io/img/p3/z-index/cat.png" alt="cat">
.dog {
  position: absolute;
  top: 10px;
  left: 100px;
  z-index: 1; /* 设置小狗图的 z-index 值大于0 */
}
.cat {
  position: absolute;
  top: 80px;
  left: 70px;
}
```

给 `.dog` 元素增加了 `z-index: 1` 属性就可以让小狗图相比于小猫图在垂直于页面的方向上离我们更近，这样效果就是小狗图显示在上面了。

![z-index](http://coding.imweb.io/img/p3/z-index/z-index-2.png)

## 层叠上下文

上面说到，z-index 默认值 `auto` 数值上等于0，那设置了 `z-index:0` 和 默认的 `z-index:auto;` 有没有区别呢？ 答案是有区别的。区别在于设置了 z-index 属性为整数值(包括0)的元素，自身会创建一个层叠上下文。而创建一个层叠上下文之后，其子元素的层叠顺序就相对于父元素计算，不会与外部元素比较。这样说比较抽象，我们来看个例子。

```html
<div class="dog-container">
  <img class="dog" src="http://coding.imweb.io/img/p3/z-index/dog.png" alt="dog">
</div>
<div class="cat-container">
  <img class="cat" src="http://coding.imweb.io/img/p3/z-index/cat.png" alt="cat">
</div>
img {
  width: 200px;
}
.dog-container {
  width: 200px;
  height: 100px;
  background: red;
  position: relative;
  z-index: auto; /* 默认值auto */
}
.dog {
  position: absolute;
  top: 10px;
  left: 100px;
  z-index: 2;
}
.cat {
  position: absolute;
  top: 80px;
  left: 70px;
  z-index: 1;
}
```

上面例子中，我们给 `.dog` 和 `.cat` 增加了容器 `.dog-container` 和 `.cat-container`, 并且 `.dog` 和 `.cat` 都设置了 `z-index`值，所以都显示在红色背景的 `.container` 之上，而且 `.dog` z-index 数值比较大，所以显示在上面。

![z-index](http://coding.imweb.io/img/p3/z-index/z-index-3.png)

但是当我们设置了 `.dog-container` 的 z-index 属性值为0之后，我们发现，z-index 值比较大的 `.dog` 元素反而到 z-index值比较小的 `.cat` 下面了

```css
.dog-container {
  width: 200px;
  height: 100px;
  background: red;
  position: relative;
  z-index: 0; /* 将 z-index 值改成0 */
}
```

效果：

![z-index](http://coding.imweb.io/img/p3/z-index/z-index-4.png)

其原因就在于我们给 `.dog-container` 设置了 `z-index:0` 之后，`.dog-container` 就创建了自己的层叠上下文，其子元素 `.dog`在比较层叠顺序的时候只会在 `.dog-container` 内比较，而不会与外面的 `.cat` 比较。如下图所示:

![z-index](http://coding.imweb.io/img/p3/z-index/z-index-5.png)

上面例子告诉我们，并不是所有情况 z-index 值大的元素都会在上面，我们在进行 z-index 比较的时候要留意其祖先元素有没有建立独立的层叠上下文，z-index 只有在同一个层叠上下文中比较才有意义。另外，对定位元素设置 z-index 属性不是唯一创建层叠上下文的方法，具有下面属性的元素都会创建层叠上下文（具体可参看：[层叠上下文 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)）：

- 根元素 (HTML)
- z-index 值不为 "auto"的 绝对/相对定位
- 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex
- opacity 属性值小于 1 的元素
- transform 属性值不为 "none"的元素，
- mix-blend-mode 属性值不为 "normal"的元素，
- filter值不为“none”的元素，
- perspective值不为“none”的元素，
- isolation 属性被设置为 "isolate"的元素，
- position: fixed
- 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值（参考[这篇文章](https://dev.opera.com/articles/css-will-change-property/)）
- -webkit-overflow-scrolling 属性被设置 "touch"的元素

也就是说，上面例子中， `.dog-container` 满足上面任意一条属性，也会一样出现上面的情况。比如设置 opacity 属性：

```css
.dog-container {
  width: 200px;
  height: 100px;
  background: red;
  position: relative;
  opacity: 0.6; /* 设置 opacity 属性小于1也会创建层叠上下文 */
}
```

效果如下：

![z-index](http://coding.imweb.io/img/p3/z-index/z-index-6.png)

## 总结

1. z-index 属性用于描述定位元素在垂直于页面方向上的排列顺序。
2. z-index 一般比较规则是值大在上，值相同则排后面的在上。
3. 元素在设置了某些属性的时候会创建层叠上下文，z-index 值比较大小只有在同一个层叠上下文才有效。

## 参考文档

- [深入理解CSS中的层叠上下文和层叠顺序](http://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)
- [理解CSS的 z-index属性](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index)
- [层叠上下文-MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)