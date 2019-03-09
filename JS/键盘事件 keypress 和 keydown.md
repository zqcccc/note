# 键盘事件 keypress 和 keydown

有个示例页面，大家试试，[链接](http://coding.imweb.io/demo/p4/event-keypress-and-keydown.html)。

- `keypress` 按字符集触发
- `keydown` 按所有键都会触发

两者设计的初衷就不同。

`keypress` 就是用来检测用户输了啥字符的，而 `keydown` 则是单纯的检测用户是否按了键盘上的按键，所以 `keypress` 常用。

两者事件对象上的 `keyCode` 值也不同。

`keyCode`是一个代码，与键盘上的一个键对应。在 `keypress` 事件中，这个 `keyCode` 还与 `ASCII`码对应，比如`keyCode` 等于 `105` ，就是按了 `i`。

最后说下，判断一个前端专业不专业，就问下他开发界面的时候有没有考虑过键盘事件。

## 参考文章

- [JavaScript Madness: Keyboard Events](http://unixpapa.com/js/key.html)
- [ASCII 对照表](http://tool.oschina.net/commons?type=4)