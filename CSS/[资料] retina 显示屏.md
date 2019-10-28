# retina 显示屏

## 关键词

如果我们去苹果的官网查看 [iPhone 7的介绍](https://www.apple.com/cn/iphone-7/specs/)，可以看到其显示屏介绍如下：

![img](http://coding.imweb.io/img/p3/retina-keywords.png)

我们以 7 plus 为例抽取如下几个关键词（这些关键词跟我们今天要说的内容都息息相关）：

- Retina HD 显示屏
- 5.5 英寸
- 1920 x 1080 像素分辨率
- 401 ppi

下面我们再来一一详细谈论这些关键词。我们先从第二个说起，因为第一个的解释需要用到下面的一些关键词。

## 5.5 英寸

这个比较简单，就是显示屏对角线的长度（我们可以通过勾股定理计算得到，甚至可以自己用尺子去量）。如下几种不同的 iPhone 的几种尺寸规格：

![img](http://coding.imweb.io/img/p3/retina-chicun.png)

注：1英寸（inch）=2.54厘米（cm）

## 1920 x 1080 像素分辨率

1920 x 1080 像素分辨率表示该显示屏的分辨率为：1920px x 1080px。

前面我们已经说过，显示屏上的每个图形都是由一个一个像素点构成的，下面的图形可以很形象的帮助你明白：

![img](http://coding.imweb.io/img/p3/retina-pixel.jpg)

既然明白了像素点，现在我们就可以解释分辨率1920px x 1080px的意思了。它表示在这个iPhone 7 plus 5.5 英寸英寸屏幕上，在竖向的高度上有1920个像素点，在横向的宽度上有1080个像素点。

注：一般来说电脑可以直接右键查看屏幕分辨率（windows操作系统），而手机可以在“设置>关于本机”中找到分辨率，如果你不知道也可以参考下面两个链接：

- [怎么查看手机分辨率](http://jingyan.baidu.com/article/7082dc1c498a0fe40a89bd2c.html)
- [怎么查看电脑屏幕的分辨率是多少](https://zhidao.baidu.com/question/504249213.html)

## 像素密度——PPI

Pixels Per Inch（PPI）也叫像素密度，所表示的是每英寸所拥有的像素数量，PPI值越高，画面的细节就会越丰富。

其计算公式如下图：

![img](http://coding.imweb.io/img/p3/retina-ppi.jpg)

简单来说就是利用我们上面所说的分辨率运用勾股定理求出对角线上的像素点，然后再把该像素点除以对角线的长度。也就得到了每英寸所拥有的像素数量。

更多关于 PPI 的资料可参看：

- [PPI（手机屏幕的PPI 和计算方法）](http://baike.baidu.com/item/PPI/9910558#viewPageContent)
- [每英寸像素](https://zh.wikipedia.org/wiki/%E6%AF%8F%E8%8B%B1%E5%AF%B8%E5%83%8F%E7%B4%A0)

## Retina HD 显示屏

所谓“Retina”是一种显示技术，可以将更多的像素点压缩至一块屏幕里，从而达到更高的分辨率并提高屏幕显示的细腻程度。这种分辨率在正常观看距离下足以使人肉眼无法分辨其中的单独像素，也被称为视网膜显示屏。

Retina 既不是指分辨率，也不是单独指PPI，而是指视觉效果。其计算公式为（可以不用了解）：

![img](http://coding.imweb.io/img/p3/retina-retina.jpg)

a 代表人眼视角，h 代表像素间距，d 代表肉眼与屏幕的距离。符合以上条件的屏幕可以使肉眼看不见单个物理像素点。这样的显示屏就可被苹果称作“Retina显示屏”。

将通常使用距离代入上公式可知：

- 手机显示器的像素密度达到或高于 300ppi 就不会再出现颗粒感（视线距离在35CM左右）
- 而苹果电脑的 Retina 显示器像素密度只要超过 200ppi 就无法区分出单独的像素（视线距离在60CM左右）

我们已经知道了 Retina 显示屏，那么 Retina HD 显示屏又是什么呢？

然而苹果官方对它自己发明的这个词也没有一个具体的概念，我们可以简单的理解为一个升级版，有着更好的显示效果就可以了。具体可参看：[来探讨一下Retina和Retina HD显示屏](http://www.feng.com/Story/To-explore-the-Retina-and-Retina-HD-display-screen_621729.shtml)

更多关于 Retina 的资料可参看：

- [Retina（一种新型高分辨率的显示技术）](http://baike.baidu.com/item/retina/4616695?fromtitle=Retina%E5%B1%8F%E5%B9%95&fromid=3782050)
- [4K 和 Retina 显示屏哪个分辨率更高？](https://www.zhihu.com/question/24935579)

## devicePixelRatio（设备像素比）

上面四个概念我们已经明白了，但是离我们的最终问题还差一点，我们接着引入第五个概念：devicePixelRatio。

在解释这个概念之前，我们先来看个 [demo（CSS 尺寸大小）](http://coding.imweb.io/demo/p3/dpr.html)。

通过 chrome 调试工具我们模拟iPhone 7（因为 iPhone 6、7的大小一样，所以实际使用iPhone 6 模拟即可），并且查看对应的 demo 元素的 CSS 宽高，会发现其宽高分别为： 375px * 667px。（该 demo 已经设置了 viewport 的宽度为 device-width，不然的话得到的宽度就是 980px 了。）。

这个宽高正好是我们前面所说 iPhone 7 分辨率（750px * 1334px） 的一半大小，这说明两者之间是存在一定关系的。而 devicePixelRatio 正好是用来描述这两者的比例关系的，下面我们将进行详细介绍。

- 物理像素：我们把分辨率的像素称之为物理像素或设备像素（如 iPhone 7 的物理像素为 750px * 1334px），它是显示设备中一个最微小的物理部件。
- 设备独立像素：CSS 的尺寸像素称之为设备独立像素（device-independent pixels 简称为“DIPs”）或 CSS 像素（如 iPhone 7 的设备独立像素为 375px * 667px），它是一个抽象的单位，主要使用在浏览器上，用来精确的度量（确定）Web页面上的内容。
- devicePixelRatio（简写 DPR）： 用来描述物理像素与设备独立像素的比例，其值等于 “物理像素 / 设备独立像素”。devicePixelRatio 值为 1 时就是我们的标准屏，值为 2 时则是我们俗称的 2 倍屏（2x），同样 3 就是 3 倍屏（3x）。

注：其实在打开 chrome 开启移动端调试的时候，我们就可以看到设备独立像素和 DPR（如果看不到 DPR，则打开右边设置，找到 device pixel ratio 即可） ，如下图：

![img](http://coding.imweb.io/img/p3/retina-mobile-debug.png)

通过上面的说明，我们可以得到 iPhone 7 的 devicePixelRatio 为 2 （750px / 375px = 2），就是 2 倍屏。那么这个 2 倍屏跟我们普通的标准屏有什么区别呢？

对一个标准屏来说，渲染一个 2px *2px 的盒子将会使用 2px* 2px 物理像素，如我们的普通电脑屏；但是对于一个 2 倍屏来说，渲染一个 2px *2px 的盒子将会使用 （2px* 2） *（2px* 2） 物理像素，如我们的 iPhone 4、5、6、7，如下图：

![img](http://coding.imweb.io/img/p3/retina-dpr.png)

注：在 3 倍屏出现以前，retina 显示屏一般就指代了 2 倍屏，所以如果是查看以往的资料，会有很多这种情况。

更多关于 devicePixelRatio 的资料可参看：

- [Window.devicePixelRatio](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/devicePixelRatio)
- [devicePixelRatio property](https://msdn.microsoft.com/zh-cn/library/dn255104(v=vs.85).aspx)

## 面临问题

熟悉了这些概念之后，我们就要开始解决需要面临的问题了。由于 devicePixelRatio 不再是1，我们的物理像素与设备独立像素不再对等，所以在实际网页开发时面临了2个问题：图片虚化、1px边框变粗

## 图片虚化

我们知道，位图（png, jpg, gif等）是由一个个像素点构成的，每个像素都具有特定的位置和颜色值，我们称之为位图像素，如下图：

![img](http://coding.imweb.io/img/p3/retina-bitmap.png)

当一个位图在标准屏显示时，一位图像素对应的就是一物理像素，这样就保证了一个完全保真的显示。但是当在 2 倍屏下时，它需要要放大四倍（宽高各两倍）来保持相同的物理像素的大小，这样就会丢失很多细节，造成失真的情形，也就是我们常说的图片虚化问题。如下图：

![img](http://coding.imweb.io/img/p3/retina-bitmap-pixels.png)

那么怎么解决该问题呢？说到底就是为了让一位图像素对应一物理像素，既然物理像素已经定了不能变，那么我们是否可以改变位图像素呢？答案是肯定的。

我们可以把要使用的图片扩大一倍，如要用的图片大小为 2px *2px，我们可以使用 4px* 4px 的图片然后设置图片大小为 2px * 2px，这样对于2倍屏则正好，而标准屏则减少像素取样（一定程度上的浪费），如下图：

![img](http://coding.imweb.io/img/p3/retina-bitmap-2x.png)

这样就解决了我们的图片在2倍屏的虚化问题，对于3倍屏也是一样的道理

## 1px 边框变粗

为了说明这个问题，我们先看一个[1px demo](http://coding.imweb.io/demo/p3/1px.html)，截图如下（iPhone 6截图）：

![img](http://coding.imweb.io/img/p3/1px-1.png)

如果我们把上图与我们手机系统上的 1px 边框进行对比，如下图：

![img](http://coding.imweb.io/img/p3/1px-2.png)

我们会发现，上面两个上下线条，下线条的粗细才是正确的，上线条就显得有点粗了。但是上线条我们是用纯正的 1px border生成的，而下线条我们实际是采用transform压缩了1px高度的一半模拟实现的，也就相当于 0.5px 的高度了。为什么会这样呢？

这是因为在2倍屏时1 CSS 像素实际对应2个物理像素, 所以为了实现真正的 1px 粗细，我们得使用 0.5px 来模拟。目前除 ios8+ 可以直接使用 0.5px 单位外，其余皆得通过模拟的办法搞定。具体实现办法有很多，大家可以参考：[7种方法解决移动端Retina屏幕1px边框问题](http://www.jianshu.com/p/7e63f5a32636)。

注：我一般使用上面 1px demo 中的方法。

## 附：iPhone 6/6s/7 plus 的 3 倍屏

我们知道 iPhone 6/6s/7 plus 的分辨率为1080px *1920px ，而其设备独立像素为 414px* 736px，如果按照我们的计算其devicePixelRatio（1080 / 414） 并不等于 3，但是我们通过`window.devicePixelRatio`或在模拟的时候都是 3。

这是因为它进行了缩放，如下图（具体可参看：[iPhone 6 Screens Demystified](https://www.paintcodeapp.com/news/iphone-6-screens-demystified) 的图形说明）：

![img](http://coding.imweb.io/img/p3/retina-7plus-3x.png)

## 参考资料

- [viewport 深入理解](https://www.cnblogs.com/2050/p/3877280.html)
- [走向视网膜（Retina）的Web时代](https://www.w3cplus.com/css/towards-retina-web.html)
- [The Ultimate Guide To iPhone Resolutions](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions)