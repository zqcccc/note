# 动画必备属性 transform

## 概述

transform 本意是变形，变换之意，在 CSS 中使用该属性可对元素进行移动（translate）、旋转（rotate）、缩放（scale）、倾斜（skew）等效果。因其有着各种特效及优良的性能，所以成为动画的标配。

在学习之前，我们可以简单欣赏下几个案例：

- [transform 简单 demo](http://coding.imweb.io/demo/p3/transform/transform.html)
- [flip card](http://desandro.github.io/3dtransforms/examples/card-01.html)
- [Cube - show sides](http://desandro.github.io/3dtransforms/examples/cube-02-show-sides.html)

## 二维（2D）变换

### translate

其语法为：`transform: translate(tx[, ty])`。其中 tx 表示 x 方向偏移，ty 表示 y 方向偏移，如果 ty 没有指定值则为0。

还可以分拆为：`transform: translateX(tx)` 或 `transform: translateY(ty)`。

简单示例如下（虚线框表示原先位置）：

```css
.box {
    transform: translate(50px, 30px);
}
```

![img](http://coding.imweb.io/img/p3/transform/translate.png)

注：tx，ty 如果为百分比值的话，其参考计算的是元素本身的宽和高，而不是父元素的宽和高。所以经常使用该方法设置定位居中，代码如下：

```css
.demo {
  position: absolute;
  top: 50%; /* 父元素高度的一半位置 */
  left: 50%; /* 父元素宽度的一半位置 */
  transform: translate(-50%, -50%); /* 元素本身的一半宽、高 */
}
```

### scale

其语法为：`transform: scale(sx[, sy])`。其中 sx 表示 x 方向的缩放比例，sy 表示 y 方向的缩放比例，如果 sy 没有指定值则与 sx 相等。

同样也可以分拆为：`transform: scaleX(sx)` 和 `transform: scaleY(sy)`

简单示例如下：

```css
.box {
    transform: scale(1.2);
}
```

![img](http://coding.imweb.io/img/p3/transform/scale.png)

### rotate

其语法为：`transform: rotate(angle)`。angle 表示顺时针角度。

简单示例如下：

```css
.box {
    transform: rotate(15deg);
}
```

![img](http://coding.imweb.io/img/p3/transform/rotate.png)

### skew

其语法为：`transform: skew(ax[, ay])`。其中 ax 表示 x 方向的顺时针角度，ay 表示 y 方向的顺时针角度，如果 ay 没有指定值则 y 方向没有倾斜。

简单示例如下：

```css
.box {
    transform: skew(30deg);
}
```

![img](http://coding.imweb.io/img/p3/transform/skew.png)

### 复合变换

上面几个变换，都可以自由组合形成更复杂的复合变换。

简单示例如下：

```css
.box {
    transform: translate(30px) rotate(10deg) skew(0, 5deg);
}
```

![img](http://coding.imweb.io/img/p3/transform/mix.png)

以上所有示例在线查看地址： [transform 简单 demo](http://coding.imweb.io/demo/p3/transform/transform.html)

### 变换中心点

默认上面所有的变换都是以元素的中心位置为参考原点的，不过我们可以通过属性 `transform-origin` 来改变参考原点。

其语法为：`transform-origin: ox oy`。其中 ox 表示 x 方向的位置，可使用 `left`、`right`、`center`、`<length>`、`<percentage>`，oy 表示 y 方向的位置，可使用`top`、`bottom`、`center`、`<length>`、`<percentage>`。如果只传入一个值，则另一个值默认为 50%

简单示例如下（在线 [demo](http://coding.imweb.io/demo/p3/transform/transform-origin.html)）：

```css
.box {
    transform: rotate(15deg);
}
.box-origin-top-left {
    transform-origin: left top;
}
.box-origin-right {
    transform-origin: right; /* 设置一个值，则另一个为50% */
}
.box-origin-px {
    transform-origin: 200px 80%;
}
```

![img](http://coding.imweb.io/img/p3/transform/origin-top-left.png)

![img](http://coding.imweb.io/img/p3/transform/origin-right.png)

![img](http://coding.imweb.io/img/p3/transform/origin-px.png)

### trasform 2D 的本质 —— 矩阵变换

上面我们已经简单了解了 transform 2D 的基本语法，接下来简单探讨下 transform 2D 的本质。

transform 2D 除了以上的四种基本语法（translate、rotate、scale、skew）外，还有一个矩阵语法，如下：

```css
transform: matrix(a, b, c, d, e, f);
```

上面 `matrix(a, b, c, d, e, f)` 的形式用矩阵符号表示，是这样的：

![图一](http://coding.imweb.io/img/p3/transform/matrix-0.png)

需要注意的是，第三行是固定的，都是 `0,0,1` ，所以 CSS 语法中省去了这一行。因为涉及到 `x`、`y` 及旋转，所以二维变换需要三个变量来表示。

下面我们具体以实例了解下矩阵是如何用来表示变换的，以上面的 translate 为例：

```css
.box {
    transform: translate(50px, 30px);
}
```

上面 css 代码的意思是说，把所有点都相对于 **原点** （通过 `transform-origin` 指定，默认为中心点），往 `x` 正方向平移 `50px`，往 `y` 正方向平移 `30px`。用数学表达式表示就是：

![transform-13](http://coding.imweb.io/img/p3/transform/matrix-1.png)

这里 tx = 50px ，ty = 30px。稍微变换一下，x' 和 x，y 都相关，写成如下的形式：

![transform-14](http://coding.imweb.io/img/p3/transform/matrix-2.png)

把两行的x、y系数取出来，再补充一行（为什么多了一行？为了运算方便，看下图也许就清楚了），得下面的语法形式—— **矩阵运算**：

![transform-14](http://coding.imweb.io/img/p3/transform/matrix-3.png)

那么，变换的本质是什么？就是把一组 `(x, y)` 通过算术运算，变为新的坐标 `(x',y')` ，刚好 **矩阵** 这种数学工具就可以用来描述这种数学变换。通用的二维矩阵变换就是：

![图二](http://coding.imweb.io/img/p3/transform/matrix-4.png)

### 常见的二维变换

上面公式的最左边的矩阵，通常是如下几种基本形式组合而成：

![basic_matrix](http://coding.imweb.io/img/p3/transform/matrix-5.png)

如果每个变换，都用 matrix 语法来表示，要写很多参数，显然这样是不合理的。所以有了四个基本的简化写法：

![css_matrix](http://coding.imweb.io/img/p3/transform/matrix-6.png)

## 三维（3D）变换

3D 变换大概有如下几种使用方法：

```css
transform: translate3d(12px, 50%, 3em);
transform: translateZ(2px);
transform: scale3d(2.5, 1.2, 0.3);
transform: scaleZ(0.3);
transform: rotate3d(1, 2.0, 3.0, 10deg);
transform: rotateX(10deg);
transform: rotateY(10deg);
transform: rotateZ(10deg);
transform: perspective(17px);
transform: matrix3d(1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0, 10.0, 11.0, 12.0, 13.0, 14.0, 15.0, 16.0);
```

3D 变换的复杂性及可以创造的特效远超 2D 变换。由于篇幅有限且深入掌握的话需要一定的想象力，这里就做不详细介绍了。

推荐两篇高质量入门文章如下：

- [CSS3 3D transform变换](http://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition/)
- [Intro to CSS 3D transforms](http://desandro.github.io/3dtransforms/)

## 参考资料

- [CSS3 Transform](https://www.w3cplus.com/content/css3-transform)
- [Advanced CSS3 2D and 3D Transform Techniques](https://www.sitepoint.com/advanced-css3-2d-and-3d-transform-techniques/)
- [transform | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform)
- [深入浅出CSS Transform](https://github.com/SamHwang1990/blog/issues/4)
- [线性代数拾遗（三）：线性变换以及矩阵的意义](http://mengqi92.github.io/2016/05/20/linear-algebra-3/)
- [理解CSS3 transform中的Matrix(矩阵)](http://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%E7%9F%A9%E9%98%B5/)
- [understanding-the-css-transforms-matrix](https://dev.opera.com/articles/understanding-the-css-transforms-matrix/)
- [transformmatrix](http://www.senocular.com/flash/tutorials/transformmatrix/)
- [css3-matrix-transform-for-the-mathematically-challenged](http://www.useragentman.com/blog/2011/01/07/css3-matrix-transform-for-the-mathematically-challenged/)