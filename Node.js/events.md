event

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

