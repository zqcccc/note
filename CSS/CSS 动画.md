### CSS 动画分为**补间动画**和**帧动画**

- 补间动画只定义开始和结束，浏览器自动补齐中间的动画，一般使用 transition
  - [transition](http://coding.imweb.io/demo/p3/transition.html)
  - 需要借助交互：
    - `:hover`, `:active`, `:checked`, `:focus`
    - `add/remove class`
  - transition-property：属性
    - 不支持动画的属性
      - background-image | float | width/height (auto -> 10px) | display | visibility | position
  - transition-duration：持续时间
  - transition-timing-function：动效
    - ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier(n, n, n, n)
    - ease-in 以慢速开始，ease-out 以慢速结束
    - 推荐 ease-out，会让人以为加载快，然后慢慢结束比较自然
    - [transition-timing-function](http://coding.imweb.io/demo/p3/transition-timing-function.html)
    - [缓动函数速查表](http://easings.net/zh-cn)
    - [cubic-bezier](http://cubic-bezier.com/)
  - transition-delay：延迟时间
- 帧动画可以定义关键帧的方式来定义动画，一般使用 animation
  - animation 既可以自动也可以借助交互，可以控制多帧，可以控制暂停
  - animation-name: 名字自定义，通过`@keyframes`来定义
  - animation-duration: 持续时间
  - animation-timing-function
  - animation-delay
  - animation-iteration-count: 执行次数，infinite 无限次
  - animation-direction: 默认 normal, alternate 来回交替
  - animation-fill-mode
    - 默认是 none
    - forwards 动画执行结束后保持最后一帧的状态
    - backwards 当动画设置了 delay 时，可以定义动画第一帧的状态
    - both 是 forwards 和 backwards 的结合
  - animation-play-state: 动画执行的状态，默认 running

![](http://ww1.sinaimg.cn/large/b7f2e8afgy1fvpe1dfksmj21630hkk08.jpg)

