## 颜色从哪来？

一般来说，你要装修房子的话，得先找装修公司设计个最终效果图，然后交给装修师傅，让他按照这个图来实现效果。

同样你做网页的时候，设计师也会给你一个最终的效果图，这个效果图可以是图片，也可以是 psd 的原稿，甚至是 sketch 原稿。那么我们的文字或背景色就来自这个效果图。

这样我们就得需要一个取色工具来帮助我们从效果图上吸取所需取色。这里推荐几个（这种工具一搜一大把）：

*   [马克鳗 - 设计稿标注、测量](http://www.getmarkman.com/)
*   [RGB网页颜色在线取色器](http://tool.ganchang.cn/getcolortool.html)
*   [ColorSchemer](http://www.colorschemer.com/colorpix_info.php)（for windows）
*   [ColorZilla](http://www.colorzilla.com/)（浏览器插件）

除此之外，打开 photoshop 和 sketch 也可以完成取色。

## 如何定义颜色？

### 颜色关键词

首先，我们可以使用一些关键词来表示颜色，如 red，green，gray 等（默认浏览器支持 147 种关键词颜色：[CSS 颜色名](http://www.w3school.com.cn/cssref/css_colornames.asp)），除此之外，还有两个关键词可用，分别是 transparent 和 currentColor。

transparent 从字面上就可以知道是透明；而 currentColor 关键字表示使用该元素 color 的计算值。如果该元素设置了 color 颜色值，则使用该 color；如果该元素没有设置 color，则继承父级元素的 color。

实例如下：

```
/* transparent */
.transparent {
    border-color: transparent;
    color: transparent;
}
/* currentColor 使用场景一 设置了color */
.box {
    color: green;    
    border-color: currentColor; /* 使用color的颜色green */
}
/* currentColor 使用场景二 没有设置color 继承父级元素的color */
.parent {
    color: red;
}
.parent .child {
    border-color: currentColor; /* 使用来自parent的red */
}

```

### rgb

表示使用**红-绿-蓝**模式来定义颜色。这其实就是我们说的十六进制（hex）和 rgb 函数两种形式。

#### 十六进制

十六进制颜色表现形式为： #RRGGBB 和 #RGB

*   “#” 后跟6位十六进制字符（0-9, A-F）
*   “#” 后跟3位十六进制字符（0-9, A-F）

其中三位数的 RGB 符号是六位数的简写形式，当六位数满足两个 R，两个 G，两个 B 同时相等的时候，就可以进行简写。如`#ff0000`、`#336699`、`#cccccc`，可省略为`#f00`，`#369`，`#ccc`，但是`#ff0122`（表示绿色的01不一样），`#3f3f3f`（表示红色的3f不一样，同样表示绿色和蓝色也不一样）则不能省略。

#### rgb

rgb 函数表示为：rgb(red, green, blue)。每个参数 (red、green 以及 blue) 定义颜色的强度，可以是介于 0 与 255 之间的整数，或者是百分比值（从 0% 到 100%）。

如`rgb(255,0,0)`表示红色，`rgb(125,125,125)`表示灰色。

#### rgba

在 rgb 的基础上，还可以添加一个 alpha 透明度表示半透明值，这样就构成了我 rgba，其函数表示为：rgb(red, green, blue, alpha)，其中 alpha 参数是介于 0.0（完全透明）与 1.0（完全不透明）的数字。

如我们需要一个半透明的黑色作为遮罩层：`background: rgba(0, 0, 0, 0.5)` 。

### hsl

除了使用**红-绿-蓝**的模式定义颜色之外，还可以通过 hue（色调）、saturation（饱和度）、lightness（亮度）模式定义颜色，其语法为：`hsl(hue, saturation, lightness)` 。

Hue 是色盘上的度数（从 0 到 360） - 0 (或 360) 是红色，120 是绿色，240 是蓝色。Saturation 是百分比值；0% 意味着灰色，而 100% 是全彩。Lightness 同样是百分比值；0% 是黑色，100% 是白色。如红色使用 hsl 表示：`hsl(0, 100%, 50%)`

下面通过图来帮助理解下：

![img](http://coding.imweb.io/img/p2/color-1.png)

![img](http://coding.imweb.io/img/p2/color-2.png)

同样对于 hsl 也可以加入透明度来表示半透明色，这就是 hsla，语法为`hsla(hue, saturation, lightness, alpha)`，alpha 取值同样为介于0.0到1.0的数字，如`hsla(0, 100%, 50%, 0.7)` 。

## 参考资料

*   [- CSS | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/color_value)
*   [CSS 合法颜色值](http://www.w3school.com.cn/cssref/css_colors_legal.asp)