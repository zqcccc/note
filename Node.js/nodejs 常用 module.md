## events

```js
const EventEmitter = require('events')
const eventEmitter = new EventEmitter()

eventEmitter.on('tutorial', (num1, num2) => {
  console.log('tutorial event has occured!')
  console.log(num1 + num2)
})

eventEmitter.emit('tutorial', 1, 2)

class Person extends EventEmitter {
  constructor(name) {
    super()
    this._name = name
  }

  get name() {
    return this._name
  }
}

let pedro = new Person('Pedro')
let christina = new Person('Christina')
pedro.on('name', () => {
  console.log('my name is ' + pedro.name)
})
christina.on('name', () => {
  console.log('my name is ' + christina.name)
})

pedro.emit('name')
christina.emit('name') // 同步执行
```

readline

```js
const readline = require('readline')
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
}) // process 全局对象，不需要引入
let num1 = Math.floor((Math.random() * 10) + 1)
let num2 = Math.floor((Math.random() * 10) + 1)
let result = num1 + num2

rl.question(`${ num1 } + ${ num2 } = ?\n`, (userInput) => {
  if(userInput.trim() == result) {
    rl.close()
  } else {
    rl.setPrompt('Incorrect response please try again\n')
    rl.prompt()
    rl.on('line', (userInput) => {
      if(userInput.trim() == result) {
        rl.close()
      } else {
        rl.setPrompt(`Your answer of ${userInput} is incorrect try again\n`)
        rl.prompt()
      }
    })
  }
})

rl.on('close', () => {
  console.log('Correct!!!')
})

```

## File System

```js
const fs = require('fs')
// create a file
// fs.writeFile('example.txt', 'this is an example', (err) => {
//   if (err){
//     console.log(err)
//   } else {
//     console.log('File successfully created')
//     fs.readFile('example.txt', 'utf-8', (err, file) => { // 默认二进制读取，所以要指定 encoding type
//       if (err) {
//         console.log(err)
//       } else {
//         console.log(file)
//       }
//     })
//   }
// })

// fs.rename('example.txt', 'example2.txt', (err) => {
//   if(err) {
//     console.log(err)
//   } else {
//     console.log('Successfully renamed the file')
//   }
// })

// fs.appendFile('example2.txt', 'Some data being appended\n', (err) => {
//   if(err) {
//     console.log(err)
//   } else {
//     console.log('Successfully appended data to file')
//   }
// })

fs.unlink('example2.txt', (err) => {
  if(err) {
    console.log(err)
  } else {
    console.log('Successfully deleted the file')
  }
})
```

## File System part 2

```js
const fs = require('fs')
// fs.mkdir('tutotrial', (err) => {
//   if(err) {
//     console.log(err) // 如果文件夹已经存在就会报错
//   } else {
//     // console.log('Successfully created folder')
//     fs.rmdir('tutotrial', (err) => { // 只能删除空文件夹
//       if(err) {
//         console.log(err)
//       } else {
//         console.log('Folder created and delete successfully!')
//       }
//     })
//   }
// })

// fs.mkdir('tutorial', (err) => {
//   if(err) {
//     console.log(err) // 如果文件夹已经存在就会报错
//   } else {
//     console.log('Successfully created folder')
//   }
//   fs.writeFile('./tutorial/example.txt', '123', (err) => {
//     if(err) {
//       console.log(err)
//     } else {
//       console.log('Successfully created file in folder')
//     }
//   })
// })

// fs.unlink('./tutorial/', (err) => {
//   if(err) {
//     console.log(err)
//   } else {
//     console.log('deleted the files in tutorial')
//   }
// })

fs.readdir('tutorial', (err, files) => {
  if(err) {
    console.log(err)
  } else {
    for(let file of files) {
      fs.unlink('./tutorial/' + file, (err) => {
        if(err) {
          console.log(err)
        } else {
          console.log(file + ' has been deleted successfully')
        }
      })
    }
  }
})
```

## Readable and Writable Streams

read and write data more efficiently

```js
const fs = require('fs')
const readStream = fs.createReadStream('./example.txt', 'utf-8')
const writeStream = fs.createWriteStream('example2.txt')
readStream.on('data', (chunk) => {
  writeStream.write(chunk)
})
```

用 stream 的方式读取文件可以用很小的内存读取很大的文件，以为它是用 chunk 的方式输出文件内容的

## Pipes and Pipe Chaining

```js
const fs = require('fs')
const zlib = require('zlib')
const gzip = zlib.createGzip()
const readStream = fs.createReadStream('./example.txt', 'utf-8')
// const writeStream = fs.createWriteStream('example2.txt')
// readStream.pipe(writeStream)
const writeStream = fs.createWriteStream('example2.txt.gz')
readStream.pipe(gzip).pipe(writeStream) // pipe chaining
```

## Http

```js
const http = require('http')
const server = http.createServer((req, res) => {
  if (req.url === '/') {
    res.write('hello world from node.js')
    res.end()
  } else {
    res.write('other domain')
    res.end()
  }
})

server.listen('3000')
```

## Serving Static Files with Http and File System Modules

```js
const http = require('http')
const fs = require('fs')
http.createServer((req, res) => {
  const readStream = fs.createReadStream('./static/index.html')
  res.writeHead(200, { 'Content-type': 'text/html' })
  readStream.pipe(res)
}).listen(3000)
```

## npm

版本号：major.minor.patch

^ 会更新 minor 或者 patch

~ 只会更新 patch

前面什么都不加锁定版本

## express（非 nodejs 官方库）

```js
// hello world
const express = require('express')
const app = express()

app.get('/', (req, res) => {
  res.send('Hello world')
})

app.listen(3000)
```

### http get request

```js
const express = require('express')
const app = express()

app.get('/', (req, res) => {
  res.send('Hello world')
})
app.get('/example', (req, res) => {
  res.send('hitting example route')
})
app.get('/example/:name/:age', (req, res) => {
  console.log(req.params)
  console.log(req.query)
  res.send(req.params.name + ':' + req.params.age)
})

app.listen(3000)
```

### static files

```js
const express = require('express')
const path = require('path')
const app = express()

app.use('/public', express.static(path.join(__dirname, 'static'))) // public 是 static 的 alias
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'static', 'index.html'))
})

app.listen(3000)
```

### http post request and body parser

```html
<form action="/" method="POST">
    <div class="form-group">
      <label for="email">Email address:</label>
      <input type="email" name="email">
    </div>
    <div class="form-group">
        <label for="pwd">Password:</label>
        <input type="password" name="password">
    </div>
    <button type="submit">Submit</button>
  </form>
```

```js
const express = require('express')
const path = require('path')
const bodyParser = require('body-parser') // npm install body-parser
const app = express()

app.use('/public', express.static(path.join(__dirname, 'static')))
app.use(bodyParser.urlencoded({ extended: false }))

app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'static', 'index.html'))
})
app.post('/', (req, res) => {
  console.log(req.body)
  // database work here
  res.send('successfully posted data')
})

app.listen(3000)
```

### json data and body parser

```html
<form action="/" method="POST" id="form">
    <div class="form-group">
      <label for="email">Email address:</label>
      <input type="email" name="email">
    </div>
    <div class="form-group">
        <label for="pwd">Password:</label>
        <input type="password" name="password">
    </div>
    <button type="submit">Submit</button>
  </form>
  <script
  src="https://code.jquery.com/jquery-3.3.1.js"
  integrity="sha256-2Kok7MbOyxpgUVvAk/HJ2jigOSYS2auK4Pfzbm7uH60="
  crossorigin="anonymous"></script>
  <script>
    $(document).ready(() => {
      $('#form').submit((e) => {
        e.preventDefault()
        $.ajax({
          url: '/',
          type: 'post',
          contentType: 'application/json',
          data: JSON.stringify($('#form').serializeArray()),
          success: (response) => {
            console.log('successfully got response')
            console.log(response)
          }
        })
      })
    })
  </script>
```

```js
app.use(bodyParser.json())
app.post('/', (req, res) => {
  console.log(req.body)
  // database work here
  // res.send('successfully posted data')
  res.json({ success: true })
})
```

### user input validation and joi

```js
const Joi = require('joi') // npm install joi
app.post('/', (req, res) => {
  // console.log(req.body)
  // database work here
  const schema = Joi.object().keys({
    email: Joi.string().trim().email().required(),
    password: Joi.string().min(6).max(12).required()
  })
  Joi.validate(req.body, schema, (err, result) => {
    if(err) {
      console.log(err)
      res.send('an error has occurred')
    } else {
      console.log(result)
      res.send('successfully posted data')
    }
  })
})
```

一个检查输入的库，正则也能做到就不多写了

### ejs

express 知道 template 在 views folder

### MiddleWare Work and Creating Custom Middleware

```js
app.use((req, res, next) => {
  console.log(req.url, req.method)
  next()
})
```

### Express Router