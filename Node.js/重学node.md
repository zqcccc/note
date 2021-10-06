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

常见的 flag 操作符

- r：表示可读
- w：表示可写
- s：表示同步
- +：表示执行相反操作
- x：表示排它操作
- a：表示追加操作

创建目录

```js
const fs = require('fs')
const path = require('path')
const {promisify} = require('util')

/* function mkDir (dirPath, cb) {
  let parts = dirPath.split('/')
  let index = 1

  function next () {
    if (index > parts.length) return cb && cb()

    let current = parts.slice(0, index++).join('/')

    fs.access(current, (err) => {
      if (err) {
        fs.mkdir(current, next)
      }else{
        next()
      }
    })
  }
  next()
}

mkDir('a/b/c', () => {
  console.log('创建成功')
}) */


// 将 access 与 mkdir 处理成 async... 风格
const access = promisify(fs.access)
const mkdir = promisify(fs.mkdir)

async function myMkdir (dirPath, cb) {
  let parts = dirPath.split('/')
  for(let index = 1; index <= parts.length; index++) {
    let current = parts.slice(0, index).join('/')
    try {
      await access(current)
    } catch (err) {
      await mkdir(current)
    }
  }
  cb && cb()
}

myMkdir('a/b/c', () => {
  console.log('创建成功')
})
```

## 模块

- 任意一个文件就是一个模块，具有独立作用域
- 使用 require 导入其它模块
- 将模块 ID 传入 require 实现目标模块定位

module 属性

- 任意js文件就是一个模块，可以直接使用module属性
- id:返回模块标识符，一般是一个绝对路径
- filename:返回文件模块的绝对路径
- loaded:返回布尔值，表示模块是否完成加载
- parent:返回对象存放调用当前模块的模块
- children:返回数组,存放当前模块调用的其它模块
- exports:返回当前模块需要暴露的内容
- paths:返回数组，存放不同目录下的node_ modules 位置

require 属性

- 基本功能是读入并且执行一个模块文件
- resolve:返回模块文件绝对路径
- extensions:依据不同后缀名执行解析操作
- main:返回主模块对象

CommonJS 规范

- CommonJS规范起初是为了弥补JS语言模块化缺陷
- CommonJS是语言层面的规范，当前主要用于Node. js
- CommonJS规定模块化分为引入、定义、标识符三个部分
- Moudle在任意模块中可直接使用包含模块信息
- Require接收标识符，
- 加载目标模块
- Exports与module. exports都能导出模块数据
- CommonJS规范定义模块的加载是同步完成

