# requestAnimationFrame

## 动画原理简介

动画的基本原理是依靠人类具有视觉暂留的特性人的眼睛看到一幅画或一个物体后，在 1/24 秒内不会消失（即每秒钟至少更换24张画面）。利用这一原理，在一幅画（一帧）还没消失前播放下一幅画（下一帧），就会给人造成流畅的视觉变化效果。

如下面的翻书动画，就是利用我们人的视觉暂留的特性的。

![img](http://coding.imweb.io/img/p5/animation.gif)

因此我们可以得出：**如果我们需要实现动画，只需要设置定时不断地绘制下一帧的画面便可以了**。

## 早期动画循环

在 `JavaScript` 中我们可以使用 `setTimeout` 和 `setInterval` 来设置延时任务。
因此在很长时间以来，计时器一直都是 `JavaScript 动画`的最核心技术。如下面的代码就是使用 `setTimeout` 方法来实现基本的动画循环：

```javascript
function animate() {
    // 动画内容
    animation1();
    animation2();
    // 间隔100ms执行动画循环
    setTimeout(function () {
        animate();
    }, 100);
}
// 执行动画
animate();
```

## 循环间隔 60Hz

早期的动画循环时候，最关键的问题是确定循环间隔的时长。一方面，循环间隔必须足够短，这样才能动画效果显得更平滑流畅；另一方面，循环时隔还要足够长，这样才能确保浏览器有能力渲染产生的变化。我们知道大多数的显示器的刷新频率是 60Hz ，即相当于在每秒钟中屏幕会重绘 60 次。因此最平滑动画的最佳循环间隔是 `1000ms/60`，约等于 16.7ms。

## setTimeout 和 setInterval 问题

然后无论是 `setTimeout` 和 `setInterval` 都并不是十分精准。

我们知道 `JavaScript` 是一个单线程的解释器，在一定时间能只能执行一段代码。为了要控制代码的执行顺序，就需要通过一个 `JavaScript任务队列` 来进行管理控制（任务会按照添加到队列的顺序执行）。通过 `setTimeout` 和 `setInterval` 我们能够设置延时多长时间把我们的代码任务添加到 `JavaScript任务队列` 中。如果当前任务队列是空的，那么添加的代码可以立即执行；如果队列不是空的，则新添加的任务需要等到其前面所有的任务都执行完成才能执行。由于前面的任务到底需要多少时间执行完，是不确定的，所以没有办法保证，`setTimeout` 和 `setInterval` 指定的任务，一定会按照预定时间执行。

如下面的代码：

```js
setTimeout(animateTask, 1000 / 60);
// 耗时长的任务
longTimeTask();
```

上面代码的 `setTimeout`，我们制定 16.7ms 后运行 `animateTask` 任务。但是，如果由于后面的 `longTimeTask` 执行（当前脚本的同步任务））非常耗时，即使过了 16.7ms 仍无法结束，那么延迟执行的 `animateTask` 就只有等着，只有等到前面的任务都运行完，才能轮到它执行。

具体可阅读下 John Resig（jQuery 作者）的这篇文章 [How JavaScript Timers Work](https://johnresig.com/blog/how-javascript-timers-work/)。

## requestAnimationFrame

由于 `setTimeout` 和 `setInterval` 的不精准问题，促使了 `requestAnimationFrame` 的诞生。 `requestAnimationFrame` 是专门为实现高性能的帧动画而设计的一个API，目前已在多个浏览器得到了支持，你可以把它用在 DOM 上的效果切换或者 Canvas 画布动画中。 `requestAnimationFrame` 并不是定时器，但和 `setTimeout` 很相似，在没有 `requestAnimationFrame` 的浏览器一般都是用setTimeout模拟。 `requestAnimationFrame` 跟屏幕刷新同步（大多数是 60Hz ）。如果浏览器支持 `requestAnimationFrame` , 则不建议使用 `setTimeout` 来做动画。

## requestAnimationFrame 的兼容使用

下面是我们常规使用 `requestAnimationFrame` 的兼容写法，当浏览器不兼容的 `requestAnimationFrame` 时则通过使用 `setTimeout` 来模拟实现,且设定渲染间隔为 `1000ms/60`。

```js
// 判断是否有 requestAnimationFrame 方法，如果有则模拟实现
window.requestAnimFrame =
window.requestAnimationFrame ||
window.webkitRequestAnimationFrame ||
window.mozRequestAnimationFrame ||
window.oRequestAnimationFrame ||
window.msRequestAnimationFrame ||
function(callback) {
    window.setTimeout(callback, 1000 / 30);
};
```

## 使用 requestAnimationFrame 实现动画

下面我们将使用 requestAnimationFrame 来实现一个物体来回移动的动画。效果如下：

![img](http://coding.imweb.io/img/p5/requestAnimationFrame.gif)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>认识Canvas</title>
</head>
<body>
    <canvas id="canvas" width="500" height="500" style="border: 1px solid #33"></canvas>
    <script>
        var canvas = document.getElementById('canvas');
        var context = canvas.getContext('2d');
        // 兼容定义 requestAnimFrame
        window.requestAnimFrame =
        window.requestAnimationFrame ||
        window.webkitRequestAnimationFrame ||
        window.mozRequestAnimationFrame ||
        window.oRequestAnimationFrame ||
        window.msRequestAnimationFrame ||
        function(callback) {
            window.setTimeout(callback, 1000 / 30);
        };

        // 绘制的圆的对象
        var circle = {
            x: 250,
            y: 250,
            radius: 50,
            direction: 'right',
            // 移动圆形
            move: function() {
                if (this.direction === 'right') {
                    if (this.x <= 430) {
                         this.x += 5;
                    } else {
                        this.direction = 'left';
                    }
                } else {
                    if (this.x >= 60) {
                         this.x -= 5;
                    } else {
                        this.direction = 'right';
                    }
                }
            },
            draw: function() {
                // 绘制圆形
                context.beginPath();
                // 设置开始角度为0，结束角度为 2π 弧度
                context.arc(this.x, this.y, this.radius, 0, 2 * Math.PI, false);
                context.fillStyle = '#00c09b';
                context.fill();
            }
        }
        // 动画执行函数
        function animate() {
            // 随机更新圆形位置
            circle.move();
            // 清除画布
            context.clearRect(0, 0, canvas.width, canvas.height);
            // 绘画圆
            circle.draw();
            // 使用requestAnimationFrame实现动画循环
            requestAnimationFrame(animate);
        }

        // 先画第一帧的圆，即初始化的圆
        circle.draw();
        // 执行animate
        animate();        
    </script>
</body>
</html>
```

