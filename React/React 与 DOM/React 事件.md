## React 事件

传统事件：小写写法、接受一个字符串

```html
<button onclick="showLog()">
    按钮
</button>
```

React 事件：驼峰式写法、接受一个函数

```jsx
<button onClick={this.logMsg}>按钮</button>
```

### React 事件函数中的 this

函数式组件没有 this，不需要额外绑定

类定义组件：

- bind 方法

1. 新增一个事件方法
2. 构造函数中为事件方法绑定 this
3. 事件绑定

```jsx
logMsg() { // 1. 新增一个事件方法
    console.log(this.state.msg)
}

constructor(props) {
    super(props)
    this.state = {
        msg: 'hello React Event'
    }
    this.logMsg = this.logMsg.bind(this) // 2. 构造函数中为事件方法绑定 this
}

render() {
    return <button onClick={this.logMsg}>打印</button> // 3.事件绑定，如果没有在 constructor 里用 bind 方法的话，这里的 this.logMsg 就要写成 this.logMsg.bind(this, ...args),logMsg 也要一并修改，但这样写貌似有性能问题
}
```

- 箭头函数

1. 新增一个事件方法
2. 使用箭头函数绑定事件

```jsx
logMsg() { // 1. 新增一个事件方法
    console.log(this.state.msg)
}

render() {
    return <button onClick={(e)=>{this.logMsg(e)}}>打印</button> // 2. 使用箭头函数绑定事件
}
```

### 事件传参

- bind 方式

事件：

`<button onClick={this.deleteRow.bind(this, ...args)}>Delete Row</button>`

bind 方式就不能在 constructor 里使用了，即使有性能问题也只能在 onClick 里面写了

第一个参数一定是 this，后面可以跟很多个参数

虽然没有写 event ，但是 event 也传递了

函数：

`deleteRow(所传参数, event) {}`

event 是事件对象的隐式传递，事件对象 event 一定要放到所传递参数的后面

- -箭头函数

事件：

`<button onClick={(e) =>{this.deleteRow(...args, e)}}>Delete Row</button>`

箭头函数不需要额外绑定 this，事件对象被当作普通参数，需要被显式的传递。

函数：

`deleteRow(所传参数, event) {}`

因为这里的 event 是显式传入的，所以这里的 event 想放哪就放哪，然后按照函数定义好的顺序传入就行了