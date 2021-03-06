table 布局被淘汰，加载慢，table 就应该是来展示数据的
CSS ZEN Garden 布局出现
*   [CSS ZEN Garden 作品1](http://www.csszengarden.com/001/)（可修改地址栏的数字查看不同的作品）
*   [CSS ZEN Garden HTML 结构](http://www.csszengarden.com/000/)
*   [CSS ZEN Garden 作品列表](http://www.mezzoblue.com/zengarden/alldesigns/)
    div + css 的布局让 div 被滥用了，所以 HTML 语义化就很重要了
    HTML 语义化的好处
1. 方便团队开发和维护
2. 有利于 SEO
3. 屏幕阅读软件（盲人）会根据结构来读页面

## 语义化

- `<nav>`：标记导航（对应网页中重要的链接群就需要用这个标签）包含在`<nav>`中间的通常是`<ul>`无序列表。当然如果是面包屑链接就需用到`<ol>`有序列表，只要记住这个语义自己灵活发挥就是可以了。

特别提示：

>HTML5规范不推荐对有辅助性的页脚链接用`<nav>`；
>HTML5中不允许将`<nav>`嵌套在`<address>`标签中。

- `<article>`：文章标记标签（表示的是一个文档、页面、应用或是网站中的一个独立的容器，原则上来讲就是聚合）。

特别提示：

>`<article>`这个标签可以嵌套使用，但是他们必须是部分与整体的关系；
>同样不能用在`<address>`标签中；
>他与`<section>`标签容易混淆所以需要注意；

- `<section>`：区块定义标签（表示的是文档或是应用的一个一般的块），他一般是有一组相似的主题的内容，一般会包含一个标题。可以用这个标签来写：文章的章节，标签式对话框中的各种标签页等等类似的功能。这里你会发现他与`<article>`这个标签很像对不对？那么怎么的开发中正确的应用他们呢？
  其实也很简单，你就这样想：如果你的页面中需要一个单独的模块来实现一个单独的功能，也就是高聚合的情况，就用`<article>`，其他的时候都用`<section>`。

- `<aside>`：定义侧栏标签（表示一部分内容与页面的主体并不是有很大的关系，但是可以独立存在），用他可以实现：升式引用、侧栏、相关文章的链接框、广告、友情链接等等。

特别提示：

>如果使用多个`<aside>`，标签应该在主要内容的后面，这样有利于SEO的搜索与提升可访问性；
>如果是与文章的主要内容有关系的图像需要用`<figure>`标签而。

- `<footer>`：页脚标签（与`<header>`标签对应的标签），用他可以实现的功能有：附录、索引、版权页、许可协议等。

特别提示：

>页脚并不一定是要位于所在元素的末尾，不过通常是这样的；
>他不可以放在`<header>`标签中，也不可以相互嵌套，或是在`<address>`元素中。

- `<strong>`表示的是重要的文本（默认为粗体显示）——重点是语义上的表达而不是展现的效果这个需要记住哦
- `<em>`表示的是强调的文本（默认为斜体）

- `<mark>`标签是HTML5中的新元素，用来突出显示文本，它的效果就像是用荧光笔给重点的语句做标记一样；

- `<span>`标签这个也是在HTML中就已经有的了，以前的文章也大概的提过一下但是没有详细的说明，下面就详细的说明一下：

  1. `<span>`没有任何的语义，所以正确的使用方式是需要在没有其它合适有标签的时候才可以用它；
  2. 它是短语级别的标签所以不会另起一行；
  3. 同`<div>`一样在一定的情况下可以添加`<span>`标签利用微格式来增加语义；
  4. 一般的情况下需要用CSS来控制它的显示样式因为它没有样式的展现样式；

`<figure>`这个元素是用来引入图表、图形、照片等，对应的场景就是像是杂志中的图片一样，在HTML5之前是没有一个专门的标签来做这个事的，之前如果实现这个功能就是用没有语义的<div>标签；

>用法提示：
>1. `<figure>`元素可以包含多个内容块，但是只能有一个`<figcaption>`（可以理解成给图表加标题）标签；
>2. 可以用`<h1>~<h6>`来给`<figure>`增加标题；
>3. `<figcaption>`不能单独出现，需要有配套的内容一起出现。

1.  文件名需要用小写字母：
    应用场景就是最简单的输入地址吧，你认为下面哪种写起来更方便，这个请自己体会吧：

    *   [http://192.168.0.1/TestHtml/The_First/Html5.html](http://192.168.0.1/TestHtml/The_First/Html5.html)
    *   [http://192.168.0.1/testhtml/the-first/html5.html](http://192.168.0.1/testhtml/the-first/html5.html)
2.  用短横线分隔单词：
    大家一定会认为，用"_"挺好，其实这是做C开发时候的写法，也可以说是习惯，但是在网站中“-”是搜索引擎更喜欢的style；
3.  使用标准的扩展名：
    现在用好多的网站都是用.htm的扩展名，这个浏览器也不会报错，但是标准的扩展名就是.html的所以大家以后写的时候就不要省时间少写一个字母“l”了。

## 让 IE8 支持 HTML5 标签
虽然默认不支持，但是我们可以通过 JS 使用 `document.createElement`来“欺骗” IE 的 CSS 引擎，让它知道某个标签的存在