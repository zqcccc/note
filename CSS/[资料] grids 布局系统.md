# grids 布局系统

我们之前有提到过网格系统，比如[960s](http://960.gs/)，[bootstrap](https://v4-alpha.getbootstrap.com/layout/grid/) 的网格系统，但是这些网格系统都是模拟出来的（使用 float 或 flexbox），而并非天生的，虽然可以解决一些常见布局问题，但面临 Win10 UI 还是有点力所不及，如下图：

![img](http://coding.imweb.io/img/p3/grid-win10.jpeg)

但是随着 CSS 的不断发展及完善，一种新的网格布局方式被纳入规范，它将会解决所有的网格问题，这就是我们要说的 CSS Grid Layout。

## 网格系统基础概念

在说 CSS Grid Layout 之前，我们先来看看 excel 的表格。

![excel css gri layout](http://coding.imweb.io/img/p3/grids-excel-1.png)

以我们的 Sheet1 的 A1 单元格为例，先是有上下左右四条线围着，然后定位是由竖直的 A 栏与横向的1行二维坐标表示 A1。如果有需要甚至还还可以和邻近的单元格合并。

现在我们提炼下上面的几个概念：线条，栏(竖直)，行(横向)，单元格，合并。接下来我们把这些概念对应到我们的网格系统：

![CSS Grid Layout](http://coding.imweb.io/img/p3/grids-excel-2.png)

- Grid Container：首先我们要设置父元素的布局为 grid，通过使用 display 属性给元素显式设置属性值grid或inline-grid，此时这个元素将自动变成网格容器，对应上图的Sheet1
- Grid Item：Item 是 container 的直接子元素，如果不考虑单元格的合并跟下面的 cell 是一样的，如果有单元格合并，在该 item 可能包括几个cell，对应上图的一个个格子，如蓝色的 A1
- Grid Lines：网格线分为水平线和垂直线，对应上图的橙色线条
- Grid Track：就是由lines构成的水平和垂直空间，对应到上图的水平和垂直灰色区域，而对于table来说就是row和column
- Grid Cell：简单来说就是单元格了，对应到上图就是蓝色的A1，而对于table来说就是单元格
- Grid Area：网格区域是由任意四条网格线组成的空间，可能由一个或多个单元格组成。对应到上图就是红色区域，相当于表格中的合并单元格之后的区域

## 网格系统基本属性

网格系统布局其实跟 flexbox 布局差不多，都是由父子元素构成的布局。所以属性分为父元素属性和子元素属性。

因篇幅有限，这里只简单介绍每个属性的用途，具体的属性取值请参考：

- [A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-items)
- [grid | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/grid)

### 父元素（Grid Container）属性

这里我们将父元素属性大概分为三大类，其中第一类与第二类属性可以简写为 `grid` 属性（不包括 `display` 属性）：

第一类：如何去定义一个网格系统，行列及间距等。

- display：grid/inline-grid，定义使用网格系统
- grid-template-columns：定义垂直栏
- grid-template-rows：定义水平行
- grid-template-areas：定义区域
- grid-column-gap：定义垂直栏与垂直栏之间的间距，如上图的A与B之间的间距
- grid-row-gap：定义水平行与水平行之间的间距，如上图的1与2之间的间距
- grid-gap：上面两个栏与行间距的缩写

第二类：自动分配形式，当定义的行或列数量不够时，多出 item 的自动排列方式：

- grid-auto-columns：定义多出的 item 的自动column的宽度大小
- grid-auto-rows：定义多出的 item 自动 row 的高度大小
- grid-auto-flow：定义自动 item 是按照先水平方向排列还是垂直方向排列

第三类：分布对齐的方式（属性跟 flexbox 的有点像）。

- justify-items：item 在水平行中的对齐方式
- align-items：item 在垂直栏中的对齐方式
- justify-content：整个水平行在 grid 范围的对齐方式，这里有个好用的 `space-evenly` 值，补足了以前flex的 `space-around`和 `space-between` 的不足
- align-content：整个垂直栏在 grid 范围的对齐方式

### 子元素（Grid Item）属性

接下来是我们的 item 属性，同样这里我也将它分为两类：

第一类：单元格所占格子多少

- grid-column-start：item 的起始栏
- grid-column-end：item 的结束栏
- grid-column：起始栏和结束栏的简写
- grid-row-start：item 的起始行
- grid-row-end：item 的结束行
- grid-row：起始行与结束行的简写
- grid-area：item所在区域

第二类：单元格的自定义对齐方式，这个跟 flexbox 的 item 有点相似。

- justify-self：自定义 item 的水平方向对齐方式
- align-self：自定义 item 的垂直方向对齐方式

## 实例演示

百说不如一练，我们接下来使用网格系统来实战下我们的 Win10 UI，如下图：

![grids demo](http://coding.imweb.io/img/p3/grids-demo-1.png))

html结构为：

```html
<div class="grid">
    <div class="item">1</div>
    <div class="item">2</div>
    <div class="item">3</div>
    <div class="item">4</div>
    <div class="item">5</div>
    <div class="item">6</div>
    <div class="item">7</div>
    <div class="item">8</div>
    <div class="item">9</div>
    <div class="item">10</div>
    <div class="item">11</div>
</div
```

写好结构后，我们就开始使用刚才说得 grid 来实现我们的效果了。先拆分成最小的单元格为 6栏 * 3行，最小单元格的大小为140px，整体内容一屏水平垂直居中。

```css
html,body {
 height: 100%;
}
.grid {
  height: 100%;
  display: grid; /* 网格布局 */

  /* 整体水平垂直居中 */
  justify-content: center;
  align-content: center;

  /* 定义6栏3行 */
  grid-template-columns: 140px 140px 140px 140px 140px 140px;
  grid-template-rows: 140px 140px 140px;

  /* 定义item之间的间距为20px */
  grid-gap: 20px;

  background: #efefef;
}
.item{
  background: #ccc;
}
```

现在我们可以看到效果如下：

![img](http://coding.imweb.io/img/p3/grids-demo-2.png)

接下来要合并单元格实现我们的最终效果。合并单元格有两种实现方式一种是 line 的开始与结束（包括 colunm 与 row），另一种就是在 grid 上面定义的 area，这里我们使用第一种方法。

这里重提下上面的 Grid Lines 概念，如要实现 n栏 * m行的网格，则需要n+1条垂直line，m+1条水平线。

第一个 item 元素单元格占了两列，第一列和第二列，那么就垂直栏开始于第一条 line，结束于第三条 line，同样第5个 item 元素也是如此

```css
.item:nth-child(1),
.item:nth-child(5) {
  grid-column: 1 / 3; /* 起始于1，结束于3 */
}
```

而第二个 item 元素栏和行都跨了两个，CSS 代码如下：

```css
.item:nth-child(2) {
  grid-column: 3 / 5; /* column起始于3，结束于5 */
  grid-row: 1 / 3;  /* row起始于1，结束于3 */
}
```

同样第七个 item 元素行跨了两个，第9个 item 元素栏跨了两个，CSS 代码如下：

```css
.item:nth-child(7) {
  grid-column: 6;
  grid-row: 2 / 4; /* row起始于2，结束于4 */
}
.item:nth-child(9) {
  grid-column: 2 / 4; /* column起始于2，结束于4 */
}
```

这个布局就这么简单的完成了，效果可见 [demo](http://coding.imweb.io/demo/p3/grid.html)

## 浏览器支持

现代浏览器最新版本基本上都支持了 CSS Grid Layout，下图是 [caniuse](http://caniuse.com/#search=grid) 上的支持情况：

![caniuse](http://coding.imweb.io/img/p3/grids-caniuse.png)

有些浏览器旧版本的已经实现但是没有默认开启（chrome < 57，firefox < 52）则需要通过下面的方式手动设置开启：

- chrome 在地址栏输入“chrome://flags”，找到"experimental web platform features"开启
- firefox在地址栏输入"about:config"，找到"layout.css.grid.enabled"开启

## 总结

上面只是 grid 布局的简单使用，实际上 grid 布局十分强大，使用起来也十分方便，未来将会是布局的主力军，但是目前由于兼容问题暂时不建议在生产环境中使用，不过我们相信很快就可以见识到 grid 布局的强大威力了。

## 参考资料

- [A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/#prop-align-items)
- [CSS Grid Layout](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout)