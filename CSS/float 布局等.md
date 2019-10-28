### 常见的三种取值：none, left, right

left， right 可以实现文字环绕的效果

float 会导致父元素高度塌陷的问题

# 清除浮动详解

清除浮动主要是为了解决由于浮动元素脱离文流导致的元素重叠或者父元素高度坍塌的问题，而这两个问题分别对应了需要清除浮动的两种种情况：清除前面兄弟元素浮动和闭合子元素浮动（解决父元素高度坍塌）。

## 清除前面兄弟元素浮动

清除前面兄弟元素浮动很简单，只需要在不想受到浮动元素影响的元素上使用 `clear:both` 即可， HTML & CSS 代码如下：

```html
<div class="fl">我是左浮动元素</div>
<div class="fr">我是右浮动元素</div>
<div class="cb">我不受浮动元素的影响</div>
.fl {
    float: left;
}
.fr {
    float: right;
}
.cb {
    clear: both;
}
```

在 CSS2 以前，clear 的原理为自动增加元素的上外边距（margin-top）值，使之最后落在浮动元素的下面。在 CSS2.1 中引入了一个清除区域（clearance）——在元素上外边距之上增加的额外间距，使之最后落在浮动元素的下面。所以如果需要设置浮动元素与 clear 元素的间距，得设置浮动的元素的 margin-bottom，而不是 clear 元素的 margin-top。

demo 可见：[clear 清除浮动](http://coding.imweb.io/demo/p3/float-clear-margin.html)

## 闭合子元素浮动

我们知道，在计算页面排版的时候，如果没有设置父元素的高度，那么该父元素的高度是由他的子元素高度撑开的。但是如果子元素是设置了浮动，脱离了文档流，那么父元素计算高度的时候就会忽略该子元素，甚至当所有子元素都是浮动的时候，就会出现父元素高度为 0 的情况，这就是所谓的父元素高度坍塌问题。为了能让父元素正确包裹子元素的高度，不发生坍塌，我们需要闭合子元素的浮动。

一般我们有两种办法可以用来闭合子元素浮动：

- 给最后一个元素设置 `clear: both`
- 给父元素新建一个 [BFC](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)(块格式化上下文)

### `clear:both`

由于我们最后一个元素使用 `clear:both`，所以该元素就能不受浮动元素影响出现在父元素的最底部，而父元素计算高度的时候需要考虑到这个正常元素的位置，所以高度自然包裹到了最底部，也就没有了坍塌。

对于这个方法，以前我们是利用新增一个空元素（`<b>` 或 `<span>` 或 `<div>` 等）来实现的，如下：

```html
<div class="container">
    <div class="box"></div>
    <span class="clear-box"></span>
</div>
.box {
    float: left;
}
.clear-box {
    clear: both;
}
```

虽然这种办法比较直观，但是不是很优雅，因为增加了一个无用的空白标签，比较冗余而且不方便后期维护（一般不太建议使用该办法）。所以后期有了通过父元素的伪元素（::after）实现的著名 clearfix 方法，代码如下：

```html
<div class="container clearfix">
    <div class="box"></div>
</div>
.clearfix::after {
    content:"";
    display:table;
    clear: both;
}
```

上面方法给父元素增加一个专门用于处理闭合子元素浮动的 `clearfix` 类名，该类使用 `::after` 伪元素类选择器增加一个内容为空的结构来清除浮动，可能你们比较疑惑的是为什么要设置 `display:table` 属性，这其实涉及到一个比较复杂的进化过程，具体可以参考资料——[clearfix浮动进化史](http://web.jobbole.com/85965/)

### 新建 BFC

该方法的原理是：父元素在新建一个 BFC 时，其高度计算时会把浮动子元素的包进来。

下面我们以实例为证：如下图我们的图片为浮动，父元素 article 的高度就出现了坍塌（没有包括图片），而根元素 HTML （默认情况下我们的根元素 HTML 就是一个 BFC）的高度则包括了图片的高度。

![img](http://coding.imweb.io/img/p3/float-clearfix-1.png)

![img](http://coding.imweb.io/img/p3/float-clearfix-2.png)

既然新建一个 BFC 可以解决父元素高度坍陷问题，那就好办了，下面这些都可以创建一个 BFC ：

- 根元素或其它包含它的元素
- 浮动 (元素的 float 不是 none)
- 绝对定位的元素 (元素具有 position 为 absolute 或 fixed)
- 内联块 inline-blocks (元素具有 display: inline-block)
- 表格单元格 (元素具有 display: table-cell，HTML表格单元格默认属性)
- 表格标题 (元素具有 display: table-caption, HTML表格标题默认属性)
- 块元素具有overflow ，且值不是 visible
- display: flow-root

虽然有这么多方法可用，可我们常用的就是 `overflow: hidden`，代码如下：

```html
<div class="container">
    <div class="box"></div>
</div
.container {
    overflow: hidden;
}
.box {
    float: left;
}
```



#### 单行省略号

```css
{
    overflow: hidden;
    white-space: nowrap; // white-space 属性设置如何处理元素内的空白,这里规定段落中的文本不进行换行
    text-overflow: ellipsis; // 
}


```



## float 布局

- [W3School](http://www.w3school.com.cn/)
- [固定宽度居中布局](http://coding.imweb.io/demo/p3/layout/float-fixed.html)
  - 内容定宽
- [内容流体布局](http://coding.imweb.io/demo/p3/layout/float-fluid.html)
  - 尽量利用所有的宽度



中间的内容是100%宽度的话，可以给它 margin ，然后让边栏的 margin-left 为负，大小为边栏本身的宽度就可以将边栏放在内容旁边了



## 网格布局系统

视频中涉及到的网址如下：

- [960s 栅格布局](http://coding.imweb.io/demo/p3/960s/demo.html)
- [960 grid system](https://960.gs/)（官网）

注：960s 是网格布局系统的鼻祖，当然随着技术的发展，基于它又发展了很多其他的网格布局系统，但是思想是想通的，无非是整体多少宽度分成几分，间距是多少，如何组合等。为什么是12列呢？因为12可以被2，3，4，6整除，这样做等分的时候是非常方便的。

注：以前设置浮动的时候，一般还需要设置`display: inline`来兼容 IE6、7，以避免出现双倍 margin 的间距。

## 元素位置--position

视频中涉及到的网址如下：

- [position](http://coding.imweb.io/demo/p3/position.html)

注：position 还有一个 `sticky` 取值，不过兼容有点问题，具体可参考：[sticky](http://caniuse.com/#search=sticky)（具体使用可参考下面的 Resources 部分链接）