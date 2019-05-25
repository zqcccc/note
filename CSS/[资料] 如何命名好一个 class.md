相信写css的人都会遇到下面的问题：

- 糟糕，怎么命名这个class，好像不太贴切，要是冲突了怎么办，要不要设计成通用一点...
- 而改别人css代码的时候则会一直有个疑问：这个class到底是只在这个地方用了，还是其他地方都用了？

于是就有了下面的做法：

- 最后终于被逼出了个class，简洁也好，中英混搭也罢，看着一头雾水也没关系，反正最后页面显示出来的。
- 这个class应该是只有这个地方用到，我可以放心写。上线之后。如果没问题，则暗自自我欣赏，看吧问题就这么简单，分分钟搞定呀；如果冲突了，则无限感慨，哎，改的时候我就隐隐不安啊，妈蛋，深坑，这是谁写的，谁写的！！！
- 不好，这个class说不定其他地方也用到了，我得加个限制范围，加个父元素？要不重新再命名个class吧，比较保险。最后如果没问题则表示还好比较机智，怎么说哥也是混过的，还是有几斤几两的；如果有问题则表示防不慎防啊，这也太太太坑了吧。

由此可见，class的命名真不是一件简单的事，尤其还要兼顾可辨别性与可读性。

## class命名到底有多难

第一，class跟id不一样，class本来就是设计用来可以重复利用的，而id才是设计唯一的（如果遵循BEM，class几乎也都是唯一的了）。

第二，样式是可以覆盖的，而且先按照权重，再按照定义的先后顺序。也许你花了十分钟设计定义的一个class样式，人家分分钟就给你干掉了，这得多恼火；也许这个页面好好的，跑到另一个页面就跟原先的样式有了冲突。

所以class命名的难就难在既要重复利用，又要避免样式的冲突。如果要重复利用，那么当然是越简单越好，越抽象可用的地方越大，太具体了就完蛋了。而如果要避免样式冲突。BEM的方式最简单，class都唯一了，那还冲突个毛线；其次就是通过父元素限定作用域，可以搞几个层级，而不是单独一个class定义样式；还有就是追加class，来实现差异化；最后不同的页面不同的文件，你用你的我用我的。

```css
// BEM
.imgslide__item__img{}

// 父元素限定
.imgslide .item .img{}

// 追加class
.img{}
.img--special{}

// 不同页面不同文件
// a.html & a.css
.img{}
// b.html & b.css
.img{}
```

总之，不管有多难，我们还是得试试去解决问题，去寻找一些规律。

## class命名的发展历程

关于class的命名，其实跟人名也差不多，如果要想别人看得懂，那关键还是在于可识别性。到目前为止class的命名大概经历了下面几个重要阶段：

- 混沌阶段，没有规则就是最好的规则
- 原子类阶段，聚集神龙现身手
- 模块阶段，以职能划分，添加前缀
- BEM阶段，规则有序

### 混沌阶段

这个没什么好说的，刚开始学html的都是这样，名字先简单的来，不够再添加1，2，3什么后缀，或者中英混搭等等，如下：

```html
h1.title
h2.title2
h2.title2-1
h2.title2-2
div.hd
div.hd-s
div.hd2
div.hd-xiangxi
```

一个字，太乱。完全无章程，规律可循，想怎么写就怎么写，写到哪里是哪里。看class去猜意思很可能就是错误的，如`.red{color:red;font-size:14px;}`，明明说好的红色，却顺带定义了个字体大小。

### 原子类阶段

这个关键在于拼凑组合，足够多的原子类拼成一个完整的样式：

```css
.fl{float:left;}
.fr{float:right;}
.pr{position:relative;}
.pa{position:absolute;}
.tal{text-align:left;}
.tac{text-align:center;}
.tar{text-align:right;}
.fs12{font-size:12px;}
div.fs12.fl.pr.tac
div.pa.tal
```

这种有两个缺点，第一是稍微复杂点的样式都要使用很多class组合，第二是如果要修改样式的时候得修改html文件，而不是css样式，而纯静态的页面是很少的，所以如果是前后端分离的，由php或后端语言渲染页面的话，改个样式还要通知后端同事去修改文件，那估计人家得疯掉。

### 模块阶段

到了这个时候，css经过这么多年的发展，页面的复杂性已经翻了好几倍了，那些无规划的混沌根本不够用，满眼的class看起来长得都差不多，后面全是1，2，3都不知道标到多少了，却不知道到底是啥区别；而原子类已经不适合频繁的修改调整更新，每更新下都是前后齐心协力。于是按职能划分的class命名规则就出现了

```html
// l表示layout,  g表示global,  m表示mod, 
.l-960
.l-left
.l-right
.g-red
.g-title
.g-price
.m-login
.m-breadcrumb
.m-slide
```

这种命名方式在一定程度解决了混乱不堪的问题，所有的按照职能划分看起来很美好，不过动不动加个前缀确实不怎么优雅，再者随着mod的增加，这个以m开头的前缀根本就不够用，于是又乱了，有加二级前缀的，也有另起前缀的。

### BEM

这个估计地球上做前端的都知道吧，实在是太火了，所以不用来解释了。优点就是你只管写你自己的，99.99%的几率不会去干掉别人的样式，class实在太长了，能一样那得多高的几率啊。缺点还是class太长，太长，太长，重要的事情说三遍。一般都记不住自己定义的class，写css的时候只好对着去拷贝。然后最痛苦的就是去修改更新，明明很简单的东西，然后你还要搞个超长的class，那叫一个烦躁，想想都懒得动手。

## 他山之石

其实每个命名的发展经历都有其特定的历史意义，也当然有其价值。所以吸取之长，弃之短缺就很好了。比喻写简单demo的时候，我们就可以用到混沌阶段的命名，足够简单，不需要纠结思考；而原子类尤其是一些简单的样式，如一行代码就可以搞定，起个class名甚是纠结，还不如直接上原子类；至于模块类，说真的应用的场景就更多了，如布局，js操作类等；而BEM我们同样可借鉴其思想，如`.class.class--name`使用`--`表示特殊化，以区分`-`。

这里我们站在前人的肩膀上，试着去开辟一条道路，可以兼顾简洁可读性及可理解辨别性。当然class的简洁肯定离不开关键词的应用，这里我们先来过一遍常见的class关键词。

### 常见class关键词：

- 布局类：header, footer, container, main, content, aside, page, section
- 包裹类：wrap, inner
- 区块类：region, block, box
- 结构类：hd, bd, ft, top, bottom, left, right, middle, col, row, grid, span
- 列表类：list, item, field
- 主次类：primary, secondary, sub, minor
- 大小类：s, m, l, xl, large, small
- 状态类：active, current, checked, hover, fail, success, warn, error, on, off
- 导航类：nav, prev, next, breadcrumb, forward, back, indicator, paging, first, last
- 交互类：tips, alert, modal, pop, panel, tabs, accordion, slide, scroll, overlay,
- 星级类：rate, star
- 分割类：group, seperate, divider
- 等分类：full, half, third, quarter
- 表格类：table, tr, td, cell, row
- 图片类：img, thumbnail, original, album, gallery
- 语言类：cn, en
- 论坛类：forum, bbs, topic, post
- 方向类：up, down, left, right
- 其他语义类：btn, close, ok, cancel, switch; link, title, info, intro, more, icon; form, label, search, contact, phone, date, email, user; view, loading...

有了关键词之后，我们先来制定一些简单的规则

### 制定简单规则：

- 以中划线连接，如`.item-img`
- 使用两个中划线表示特殊化，如`.item-img.item-img--small`表示在`.item-img`的基础上特殊化
- 状态类直接使用单词，参考上面的关键词，如`.active, .checked`
- 图标以`icon-`为前缀（字体图标采用`.icon-font.i-name`方式命名）。
- 模块采用关键词命名，如`.slide, .modal, .tips, .tabs`，特殊化采用上面两个中划线表示，如`.imgslide--full, .modal--pay, .tips--up, .tabs--simple`
- js操作的类统一加上`js-`前缀
- 不要超过四个class组合使用，如`.a.b.c.d`

关键词及规则都有了，现在可以进入本文的核心的核心，实战操作。

### 实战操作

以布局入手，大概结构如下：

```html
header.header>.inner-center
section.section-feature>.inner-center // if
section.section-main>.inner-center
section.section-postscript>.inner-center // if
footer.footer>.inner-center
```

具体可参考[HTML整站结构设计](http://imweb.io/topic/55e1d253771670e207a16bb2)，这里我们可以看出都是些简单的关键词，比较好理解，一看就知道是什么。

现在问题来了，如果其他地方也要用到这些关键词怎么办？

**修饰关键词**

以header为例，我们可以添加前缀表示不同的header，如区块头部`.block-hd`(hd为header简写)，modal头部`.modal-hd`，文章头部`.article-hd`。

同样标题也可以分为，页面标题`.page-tt`(title的简写)，区块标题`.block-tt`等。

同样，这给我们提出了第二个问题，如果要特殊化某个class该怎么办？

**特殊化class**

以上面的tt为例，大概有三种办法：

第一种犯法：直接修改class，将`.page-tt`修改成`.page-user-tt`(可以采用scss的`%`先定义共用的代码)。

第二种办法： 追加class特殊化，根据我们上面定义的规则，在`.page-tt`上追加一个class成为`.page-tt.page-tt--user`，注意`.page-tt--user`不是一个独立的class，它使基于`.page-tt`这个基础上的。

第三种办法： 使用父类，给一个范围，于是形成`.page-user .page-tt`。

一般我们使用的是第二种和第三种办法，因为这两种都有共同的`.page-tt`，可以比较方便控制一些基础共有的样式。

由第三个通过父类控制的办法，我们进入第三个要讨论的问题，层级结构

**层级**

最适合层级的例子莫过于`ul>li`结构了，如下面的结构：

```html
<ul>
 <li>
  <a href="#"><img src="" alt=""></a>
  <h3><a href="#"></a></h3>
  <p></p>
 </li>
</ul>
```

一般来说我们也有两种办法定义层级，第一种为继承式，第二种为关键词式。

```css
// 继承式
ul.card-list
 li.list-item
     a.item-img-link>img.item-img
        h3.item-tt>a.item-tt-link
        p.item-text

// 关键词式
ul.card-list
 li.item
     a.field-img-link>img.field-img
        h3.field-tt>a.field-tt-link
        p.field-text
```

由上可以看出继承式一般子元素接着父元素的最后一个单词如li接着ul的`list`，而li的子元素接着li的`item`；至于关键词式则完全由关键词来表示层级，`list>item>filed`正好构成三层等级。

最后由我们的层级进入我们最后一个问题，如何控制样式的范围

**样式范围**

这里以[腾讯课堂](http://ke.qq.com/)的课程详细页右边栏为例，如下图：

![img](http://imweb-io-1251594266.file.myqcloud.com/Fg2kYjYlEDfE379HWSWDo1j7gKtE)

三个区块的基础框架为：

```html
.aside-block.block--xxx>
 h3.block-tt
    .block-bd
```

其中`.aside-block.block--xxx`用到了我们的特殊化class，而`.block-tt`,`block-bd`则使用了我们的修饰关键词，至于`.aside-block`与它的子元素之间则使用了我们上面说的继承式层级。现在根据这个层级结构我们定义基础样式如下：

```css
.aside-block{
 .block-tt{}
    .block-bd{}
}
```

假设这里的联系机构区块的标题不一样，我们则可以：

```css
.block--contact{
 .block-tt{}
}
```

当然如果本身有5个区块，2个标题一样，另外三个标题又一样，也许我们就有需要给`.block-tt`追加一个特殊化class，或者给`aside-block`特殊化一个class，如：

```css
.aside-block{
 .block-tt{
     &.block-tt--special{}
    }
    .block-bd{}
}
// 或
.aside-block{
 &..aside-block--special{
      .block-tt{}
  }
}
```

基础框架讨论完毕之后，就轮到我们的内容了，以联系机构为例：

使用`ul>li`结构，所以样式是另外一个独立的范围，不嵌套在之前的`.aside-block`里面，html及css代码如下：

```html
ul.contact-list
 li
     i.item-icon.icon-font.i-xxx
        a.item-tt
        p.gray
```

这里我们li没有设置class，而p使用了一个全局的class`.gray`

```css
.gray{}
.contact-list{
 li{}
    .item-icon{}
    .item-tt
}
```

同理如果我们有其他地方应用这个可以拷贝过去，而如果需要一点调整，我们可以使用父元素来控制，如这里我们可以使用`.block--contact`来进一步调整contact的样式，如：

```css
.block--contact{
 .contact-list{
     li{}
    }
}
```

至此，我们的class命名方法讨论完毕，说到底就是先记住一些必备的基础关键词，然后合理应用上刚才提出的修饰关键词，特殊化class，层级及最后的样式范围就可以了。