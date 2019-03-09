## 水平间隙问题

我们创建一个导航列表，并将其列表 item 设置为 inline-block，主要代码如下：

```html
<div class="nav">
  <div class="nav-item"><a>导航</a></div>
  <div class="nav-item"><a>导航</a></div>
  <div class="nav-item"><a>导航</a></div>
</div>
.nav {
  background: #999;
}
.nav-item{
  display:inline-block; /* 设置为inline-block */
  width: 100px;
  background: #ddd;
}
```

效果图如下：

![img](http://coding.imweb.io/img/p3/inline-block-demo.png)

这里的空隙是因为我们编写代码时输入空格、换行都会产生空白符。而浏览器是不会忽略空白符的，且对于多个连续的空白符浏览器会自动将其合并成一个，故产生了所谓的间隙。对于上面实例，我们在列表 item 元素之间输入了回车换行以方便阅读，而这间隙正是这个回车换行产生的空白符。

同样对于所有的行内元素（inline，inline-block），换行都会产生空白符的间隙。

### 消除空白符

#### 1. 代码不换行

```html
<div class="nav">
  <div class="nav-item">导航</div><div class="nav-item">导航</div><div class="nav-item">导航</div>
</div>
```

考虑到代码可读以及维护性，我们一般不建议连成一行的写法

#### 2. 设置 font-size

首先要理解空白符归根结底是个字符，因此，我们可以通过设置 font-size 属性来控制产生的间隙的大小。我们知道如果将 font-size 设置为 0，文字字符是没法显示的，那么同样这个空白字也没了，间隙也就没了。

于是顺着这个思路就有了另一个解决方案：通过设置父元素的 font-size 为 0 来去掉这个间隙，然后重置子元素的 font-size，让其恢复子元素文字字符。

所以该方法代码如下：

```css
.nav {
  background: #999;
  font-size: 0; /* 空白字符大小为0 */
}
.nav-item{
  display:inline-block;
  width: 100px;
  font-size: 16px; /* 重置 font-size 为16px*/
  background: #ddd;
}
```

使用该方法时需要特别注意其子元素一定要重置 font-size，不然很容易掉进坑里（文字显示不出来）。

## 对齐问题

由于 inline-block 属于行内级元素，所以 [vertical-align](https://developer.mozilla.org/zh-CN/docs/Web/CSS/vertical-align) 属性同样对其适用。

在正式讲解 vertical-align 之前，我们需要先说一些基本概念。

### 中线、基线、顶线、底线

中线（middle）、基线（baseline）、顶线（text-top、底线（text-bottom））是文本的几个基本线，其对应位置如下图：

![img](http://coding.imweb.io/img/p3/inline-block-baseline.png)

- 基线（base line）：小写英文字母`x`的下端沿。
- 中线（middle line）：小写英文字母`x`的中间。
- 顶线（text-top）：父元素 font-size 大小所组成的一个内容区域的顶部
- 底线（text-bottom）：父元素 font-size 大小所组成的一个内容区域的底部

### vertical-align 的值

vertical-align 只接受8个关键字、一个百分数值或者一个长度值。下面我们将看看各关键字如何作用于行内元素。

| 值             | 描述                                               |
| -------------- | -------------------------------------------------- |
| baseline       | 默认元素的基线与父元素的基线对齐。                 |
| sub            | 将元素的基线与其父元素的下标基线对齐。             |
| super          | 将元素的基线与其父代的上标 - 基线对齐。            |
| text-top       | 将元素的顶部与父元素的字体顶部对齐。               |
| text-bottom    | 将元素的底部与父元素的字体的底部对齐。             |
| middle         | 将元素的中间与基线对齐加上父元素的x-height的一半。 |
| top            | 将元素的顶部和其后代与整行的顶部对齐。             |
| bottom         | 将元素的底部和其后代与整行的底部对齐。             |
| `<length>`     | 将元素的基线对准给定长度高于其父元素的基线。       |
| `<percentage>` | 像<长度>值，百分比是line-height属性的百分比。      |

具体 demo 可参考：[行内级元素垂直对齐方式](http://coding.imweb.io/demo/p3/vfm/ifc-vertical-align.html)