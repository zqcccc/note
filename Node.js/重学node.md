# 重学 Node.js

非阻塞异步 io 事件驱动

全局对象 global

```js
console.log(this == global) // false

(function () {
  console.log(this == global) // true
})()
```

```javascript
// 其他全局属性或方法
__filename
__dirname
timer 类函数
process
require
module
exports
```

全局变量 Buffer

Buffer 让 JavaScript 可以操作二进制

中文是 3 个字节

```js
// 静态方法
Buffer.concat
Buffer.isBuffer
```

自定义 split 方法

```js
ArrayBuffer.prototypr.split = function (sep) {
	let len = Buffer.from(sep).length
  let ret = []
  let start = 0
  let offset = 0
  
  while(offest = this.indexOf(sep, start) !== -1) {
    ret.push(this.slice(start, offset))
    start = offset + len
  }
}
```

在 node 中拷贝文件的方法都是基于 readFile 和 writeFile 这种一次性读到内存中的方式，不方便大文件的操作，那我们就来实现一个大文件的读写

```js
const fs = require('fs')

let buf = Buffer.alloc(10)

const BUFFER_SIZE = buf.length
let readOffset = 0

fs.open('a.txt', 'r', (err, rfd) => {
  fs.open('b.txt', 'w', (err, wfd) => {
  	function next() {
      fs.read(rfd, buf, 0, BUFFER_SIZE, readOffset, (err, readBytes, buffer) => {
        if(!readBytes) {
          fs.close(rfd, () => {})
          fs.close(wfd, () => {})
          console.log('拷贝完成')
          return
        }
        readOffset += readBytes
        fs.write(wfd, buf, 0, BUFFER_SIZE, (err, written) => {
          next()
        })
      })
    }
    next()
  })
}) 
```

上面是一种回调的写法，其实 node 中更推荐的是流操作

