canvas 是画布元素

```html
<canvas id="canvas" width="500" height="500"></canvas>
<script>
// 获取指定的 canvas 元素
var canvas = document.getElementById('canvas')
// 调用 canvas 元素的 getContext 方法访问获取 2d 渲染上下文
var context = canvas.getContext('2d')
// 通过 2d 渲染上下文执行绘画操作来绘制图形
context.绘画操作()
</script>
```

canvas 坐标系统，向右和向下建立坐标系，向右的是 x 轴，值为正，向下的是 y 轴

### 矩形

两种方法绘制矩形：

- [绘制矩形的轮廓 strokeRect(x, y, width, height)](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/strokeRect)
- [绘制填充矩形 fillRect(x, y, width, height)](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/fillRect)

x, y 是所绘制的矩形左上角的坐标

```js
context.strokeRect(20, 120, 120, 60)
```

### 线条

- beginPath
- moveTo
- lineTo
- closePath
- fill | stroke

```js
// 绘制了一条直线
// 开始设置路径
context.beginPath()
// 设置路径远点
context.moveTo(10, 20)
// 设置路径到达的点
context.lineTo(50, 20)
// 输出路径轮廓
context.stroke()

// 绘制了三角形
context.beginPath()
context.moveTo(100, 50)
context.lineTo(100, 200)
context.lineTo(250, 200)
context.lineTo(100, 50)
// 这里写 context.closePath() 就可以少写一句 lineTo，直接闭合路径
context.stroke() // 空心填充
// context.fill() 实心填充
```

### 文本

#### font

在 Canvas 中我们可以通过使用 `font` 属性 来设置或返回文本内容的当前字体属性。
而 `font` 有个默认值，是 '10px sans-serif'。

需注意的是：要设置 `font` 的属性值时，必须至少包含两个值，即"**字体大小** **字体名称**"，如果没有这两个值的任何一个，那么设置就不会生效。

- [fillText(text, x, y [, maxWidth\])](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Drawing_text)
  在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的
- [strokeText(text, x, y [, maxWidth\])](https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Drawing_text)
  在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的

具体参考代码如下：

```javascript
// 绘制描边字体
context.font = '30px arial';
context.strokeText('描边字体', 50, 50);
// 绘制填充字体
context.font = 'italic 60px serif';
context.fillText('填充字体', 50, 100);
```

### 设置简单样式

#### 上色

- context.fillStyle = color 填充颜色
- context.strokeStyle = color 描边颜色

color 要符合 CSS3 颜色标准

#### 修改线宽

context.lineWidth = number 在绘制线条的代码之前写就行了

#### 参考

- [fillStyle](http://www.w3school.com.cn/tags/canvas_fillstyle.asp)
  属性设置或返回用于填充绘画的颜色、渐变或模式
- [strokeStyle](http://www.w3school.com.cn/tags/canvas_strokestyle.asp)
  属性设置或返回用于线条轮廓的颜色、渐变或模式。
- [lineWidth](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/lineWidth)
  设置线段厚度的属性（即线段的宽度）

### 擦除 Canvas

[clearRect(x, y, width, height)](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/clearRect) 设置指定矩形区域内（以 点 (x, y) 为起点，范围是(width, height) ）所有像素变成透明，并擦除之前绘制的所有内容的方法。

### 加载图像

#### 创建图片对象

需要创建一个图片对象，并设置图片加载地址和监听其加载完成（onload）的事件

具体参考代码如下：

```js
// 加载图像
var image = new Image();
// 设置图片加载 onload 事件
image.onload = function() {
  // 图片加载完成
}
// 设置图片加载地址
image.src = 'https://ke.qq.com/classroom/assets/lib/img/canvas-logo.png';
```

#### 绘制加载好的图片到画布上

在 image 的 `onload` 事件回调中，加入下面的代码

```javascript
context.drawImage(image, 50, 50, 100, 100);
```

完整例子如下：

```js
var canvas = document.getElementById('canvas')
var context = canvas.getContext('2d')

var image = new Image()
image.src = 'http://coding.imweb.io/img/p5/plane.png'

image.onload = function() {
    context.drawImage(image, 50, 50)
}
```

### 动画循环

- 初始化
- 动画循环（更新、清楚、绘制）
- 结束动画循环

```js
var canvas = document.getElementById('canvas')
var context = canvas.getContext('2d')
var monster = {
    x: 30,
    y: 50,
    size: 60
}

var image = new Image()
image.src = 'http://coding.imweb.io/img/p5/monster.png'
// 图片加载完成
image.onload = function() {
    context.drawImage(image, monster.x, monster.y, monster.size, monster.size)
    animate()
}

function animate() {
    if (moster.x > 400) {
        return
    }
    monster.x += 5
    context.clearRect(0, 0, cavas.width, canvas.height)
    context.drawImage(image, monster.x, monster.y, monster.size, monster.size)
    requestAnimationFrame(animate)
}
```

