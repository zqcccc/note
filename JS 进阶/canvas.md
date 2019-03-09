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

