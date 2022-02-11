fs.copyFile 是把整个文件读到内存中再写到新的目标里，这种操作并不适合大文件

流操作 copy 可用于大文件的拷贝

```js
const fs = require('fs')

/**
 * 01 打开 a 文件，利用 read 将数据保存到 buffer 暂存起来
 * 02 打开 b 文件，利用 write 将 buffer 中数据写入到 b 文件中
 */
let buf = Buffer.alloc(10)

// 01 打开指定的文件
/* fs.open('a.txt', 'r', (err, rfd) => {
  // 03 打开 b 文件，用于执行数据写入操作
  fs.open('b.txt', 'w', (err, wfd) => {
    // 02 从打开的文件中读取数据
    fs.read(rfd, buf, 0, 10, 0, (err, readBytes) => {
      // 04 将 buffer 中的数据写入到 b.txt 当中
      fs.write(wfd, buf, 0, 10, 0, (err, written) => {
        console.log('写入成功')
      })
    })
  })
}) */

// 02 数据的完全拷贝
/* fs.open('a.txt', 'r', (err, rfd) => {
  fs.open('b.txt', 'a+', (err, wfd) => {
    fs.read(rfd, buf, 0, 10, 0, (err, readBytes) => {
      fs.write(wfd, buf, 0, 10, 0, (err, written) => {
        fs.read(rfd, buf, 0, 5, 10, (err, readBytes) => {
          fs.write(wfd, buf, 0, 5, 10, (err, written) => {
            console.log('写入成功')
          })
        })
      })
    })
  })
}) */

const BUFFER_SIZE = buf.length
let readOffset = 0

// 流操作
fs.open('a.txt', 'r', (err, rfd) => {
  fs.open('b.txt', 'w', (err, wfd) => {
    function next () {
      fs.read(rfd, buf, 0, BUFFER_SIZE, readOffset, (err, readBytes) => {
        if (!readBytes) {
          // 如果条件成立，说明内容已经读取完毕
          fs.close(rfd, ()=> {})
          fs.close(wfd, ()=> {})
          console.log('拷贝完成')
          return
        }
        readOffset += readBytes
        fs.write(wfd, buf, 0, readBytes, (err, written) => {
          next()
        })
      })
    }
    next()
  })
})
```

