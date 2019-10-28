## flex 基础

![](http://ww1.sinaimg.cn/large/b7f2e8afgy1fvpe60dy02j210m0ia7a0.jpg)

- flex container 相关属性 [Flexbox Playground](https://demos.scotch.io/visual-guide-to-css3-flexbox-flexbox-playground/demos/)
  - flex-direction 主轴方向
    - row / row-reverse / column / colunm-reverse
  - flex-wrap 换行
    - nowrap / wrap / wrap-reverse
  - justify-content 主轴方向上 item 的对齐方式
    - flex-start / flex-end / center / space-between / space-around
  - align-items 每个 item 的 cross axis 方向的显示方式
    - stretch / flex-start / flex-end / center / baseline
  - align-content 是 cross axis 方向上每行的行对齐方式
    - stretch / flex-start / flex-end / center / space-between / space-around
  - flex-flow 是 flex-direction 和 flex-wrap 的简写
- flex item 相关属性
  - order 越大的排后面，默认0
  - flex-grow 分配多余的空间，默认0
  - flex-shrink 当空间不足时压缩 item，默认1
  - flex-basis 宽度，当有 width 时，flex-basis的优先级要高
  - align-self 是 item 在交叉轴的对齐方式，会覆盖 align-content 的属性
  - flex 是 flex-grow, flex-shrink, flex-basis 的简写
    - `flex: none;` 是`flex: 0 0  auto;`的简写
    - `flex: 1;` 是`flex: 1 1  auto;`的简写
    - [flex值的区别](https://segmentfault.com/q/1010000004080910/a-1020000004121373)

- [flexbox](http://coding.imweb.io/demo/p3/flexbox.html)

## flexbox 剩余空间分配规则

前面我们学习到了 flexbox 布局。通过使用 flexbox 布局，我们可以更轻松实现以往很难实现的页面布局。本文主要讲解 flexbox 布局是如何去分配和计算布局剩余空间的。（本文阅读前要求你对 flexbox 已经有了初步的认知，如果不是很了解，建议先学习前面视频内容。）

## 基础概念

为了更好地理解本文内容，我们需要先了解下面一些基础概念。

### flexbox 容器 (flexbox container)

flexbox 容器又称弹性容器，通过设置 `display: flex` 而产生，简单示例如下：

```css
.container {
  display: flex; /* 或者 inline-flex */
}
```

### flexbox 项目 (flexbox item）

当设置一个元素为 flexbox 容器时，其直接子元素将自动成为容器成员，也可以称之为：flexbox 项目。

注：因为 flexbox 布局是发生在父元素和子元素之间的，所以下面为了行文方便，统一将 flexbox 容器称为“父容器”，而 flexbox 项目统一称为“子元素”

### 剩余空间

剩余空间就是指父容器在主轴方向上剩余未分配的空间，它是 flexbox 布局中一个很重要的词。我们可以借助下面的例子来更好地理解：

![img](http://coding.imweb.io/img/p3/flexbox-leftsize.png)

```html
<div class="container" width="600px" style="display:flex;">
  <span class="item1" width="200px">item1</span>
  <span class="item2" width="200px">item2</span>
</div>
```

上面代码，我们定义了一个宽度为600px的父容器 container，以及宽度为200px的子元素 item1 和 item2 ，那么我们得出其剩余空间为200px，计算公式为：

```
剩余空间 = 父容器的宽度 - item1的宽度 - item2的宽度
```

## 剩余空间分配相关属性

flexbox 布局中的子元素可以通过设置 `flex` 属性来改变其所分配到的空间大小。`flex` 属性包括了 `flex-basis`、 `flex-grow`、`flex-shrink`

### flex-basis

flex-basis 用来定义子元素的默认宽或高。如果父容器 `flex-direction` 属性的方向为水平方向则为宽度，如为垂直方向则为高度。相当于给子元素设置宽或高。如果同时设置了该属性与宽或高，则该属性权重大于宽或高的值。

### flex-grow

flex-grow 用来指定父容器多余空间的分配比率，默认值为0。看到这里，大家可能还是没有概念。为了更形象地理解，我们一起看下下面的例子。

#### 例子： 只设置 item1 的 flex-grow 为1

![img](http://coding.imweb.io/img/p3/flexbox-grow.png)

其 HTML 代码如下：

```html
<div class="container">
  <span class="item item-flex-grow">item1</span>
  <span class="item item2">item2</span>
</div>
```

其 CSS 代码如下：

```css
/* css 部分 */
.container {
  display: flex;
  width: 600px;
  height: 140px;
  align-items: center;
  background-color: #ddd;
}
.item {
  width: 200px;
  height: 120px;
  line-height: 120px;
  text-align: center;
  background-color: orange;
}
.item2 {
  background-color: green;
}
.item-flex-grow{
  flex-grow:1;
  background-color: 
}
```

这里我们对 item1 设置`flex-grow:1` 后，我们可以看到 item1 的所占空间宽度变成400px，也就是说 item1 把之前我们所说的父容器剩余的200px空间都占用了。

在这里我们可以得出：**其实 flex-grow 即定义如何去分配父容器的剩余空间** ，当值为0时，则子元素都不会索取父容器的剩余空间。当 item1 设置 `flex-grow: 1` 的时候，由于 item2 没有设置 flex-grow 的值，则剩余空间将会被分成一份，并且分别分给了 item1。

#### 例子： 设置 item1 的 flex-grow 为1，且 item2 的 flex-grow 为3

如果此时我们设置 item2 的`flex-grow:3` ，item2 也将会参与索取父容器的剩余空间，此时父容器的剩余空间将分为4份，然后1份分配到 item1，而分配3份到 item2，如下图：

![img](http://coding.imweb.io/img/p3/flexbox-grow2.png)

#### 如果子元素的宽度的总和超过父容器，flex-grow 将不生效。

上面的例子，我们只考虑了子元素的宽度总和都是没有超过父容器的宽度的情况，则其可以使用 flex-grow 来分配父容器的剩余空间。那么当子元素的宽度总和超过父容器的宽度时，这时剩余空间还可以分配吗？此时 flex-grow 是否还有效呢？让我们先看看下面的例子：

这里我们设置上面例子的 item1 和 item2 的宽度为350px，则子元素的宽度总和为700px且超过父容器container的600px宽度。

![img](http://coding.imweb.io/img/p3/flexbox-grow3.png)

我们可以看到 flex-grow 并没有起到作用，且两个 item 的宽度还被压缩到只有300px。

通过之前学到的如何计算剩余空间的方法，我们可以算出本例子的剩余空间为600px - 700px即 -100px,这里可以得出由于没有剩余空间，则定义了 flex-grow 的子元素能分配到的空间为0，故不生效。另外我们需要知道的是 flexbox 环境的父容器的宽度600px并不会因为子元素的总宽而改变，即子元素的宽度总和最多等于父容器的宽度，所以为了让子元素完整显示在父容器内，只有两个办法：

- 通过设置 flex-wrap 来使子元素换行
- 通过压缩子元素来使其能容纳在父容器内

### flex-shrink

flex-shrink 用来指定父容器空间不够时子元素的缩小比例，默认为1。如果一个 flexbox 项目的 flex-shrink 属性为0，则该元素不会被压缩。

#### 为什么需要 flex-shrink 来定义缩小比例呢？

上面我们可以知道，当子元素的宽度总和大于 flexbox 父容器的宽度时，其剩余空间将为负数，如果没有设置换行的情况下，其将会通过压缩子元素来使其能够容纳在父容器内。那么我们如何控制子元素的压缩比例呢？答案就是通过将通过设置 `flex-shrink` 这个属性。

#### 例子：设置项目的flex-shrink

下面例子，我们设置两个 item 的宽度为350px，而容器 container 的宽度仍为600px。同时定义了 item1 和 item2 的 flex-shrink 的属性分别为1和4。如下所示：

![img](http://coding.imweb.io/img/p3/flexbox-shrink.png)

代码如下：

```css
/* 这里只展示关键css */
.container {
  display: flex;
  width: 600px;
  height: 140px;
}
.item {
  width: 350px;
  height: 120px;
}
.item1 {
  flex-shrink: 1;
}
.item2{
  flex-shrink: 4;
}
```

我们看到由于缺少100px的空间，按照 item1 和 item2 定义的 flex-shrink 的值，缺少的100px将分成5份。item1 将压缩其中的 1/5 即20px，item2 的将压缩其中的 4/5 即80px。

#### 例子：设置项目的 flex-shrink 为0

在上面的知识中，我们了解到 flex-shrink 默认值为1，即默认子元素在父容器空间不足时会被压缩。现在我们把项目的 flex-shrink 设为0来看下不压缩的情况。如下所示：

![img](http://coding.imweb.io/img/p3/flexbox-shrink0.png)

代码如下：

```css
/* 这里只展示关键css */
.container {
  display: flex;
  width: 600px;
  height: 140px;
}
.item {
  width: 350px;
  height: 120px;
  flex-shrink: 0;
}
```

## 更多阅读

- [flex 计算规则在线demo](http://coding.imweb.io/demo/p3/flexbox-sizerule.html)
- [Flex MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/flex)
- [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)