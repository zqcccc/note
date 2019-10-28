## 组件声明

ES6 class 声明

```jsx
export default class Main extends React.Component {
    render() { // 通过 render 方法返回一个 React 元素
        return <div>Hello, React</div>
    }
}
```

## 组件使用

基于 DOM 标签的 React 元素

```js
const element = <div>Hello react</div>
```

基于自定义组件的 React 元素

```js
const element
```

## 组件的属性 props

属性的传递

```jsx
const element = <Hello name="React" /> // JSX 属性会被当成一个对象传到组件中
```

属性的接受

```jsx
export default class Main extends React.Component {
    render() {
        return <div>Hello {this.props.name}</div>
    }
}
```

重点：**组件的数据流：Props 是只读的**

react是**单向数据流**，所以props是不能直接修改的，只能通过父组件传递新的属性以进行属性的更新，子组件无法直接更新 props，但是可以通知父组件传递新属性以实现属性的更新，父组件传递新的属性涉及到组件状态state的更新，state的更新会触发父组件的重新渲染

拓展：可以在父组件里写子组件的内容，然后子组件用 `{this.props.children}` 访问

## 组件的组合与提取

## 组件的状态 - State

组件是一个状态机

组件（state）= view

State 的声明和使用

this.state只能在构造函数中初始化

```jsx
export default class Main extends React.Component {
    constructor (props) { // 1.添加类的构造函数
        super(props)
        this.state = { // 2.初始化状态 this.state
            name: 'React'
        }
    }
    
    render() {
        retrun <div>Hello {this.state.name}</div> // 3. 使用 state
    }
}
```

State 的更新

直接修改this.state对象不会触发组件的render，无法更新视图，必须使用setState方法才会触发组件的重新渲染，组件的数据自顶向下流动，组件state也可作为子组件的属性进行传递，而上层组件并不需要关心下层组件的状态

```jsx
export default class Main extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            clock: new Date() // 初始化一个时钟状态
        }
    }
    
    componentDidMount() {
        this.interval = setInterval(() => { // 挂载后新增一个时钟定时器
            this.setState({ // 定时器中使用 setState 方法更新 state
                clock: new Date()
            }, 1000)
        })
    }
    
    componentWillUnmount() {
        clearInterval(this.interval)
    }
    
    render() {
        return <div>当前时间：{this.state.clock.toLocaleTimeString()}</div>
    }
}
```

## 组件的生命周期

三个阶段

1. 装载过程


  - getDerivedStateFromProps() componentDidMount()


2. 更新过程

  - getDerivedStateFromProps() shouldComponentUpdate()
  - getSnapshotBeforeUpdate() componentDidUpdate()

3. 卸载过程

- componentWillUnmount()

### 大致流程

创建时

1. constructor
2. getDerivedStateFromProps(props, state) 组件创建后（DOM 未创建，静态方法没有实例无法访问 this，与组件状态无关，传入一个 props 就会生成一个新的 state）
3. render
4. render 更新 DOM
5. componentDidMount 组件完成实例化（挂载完成仅调用一次）

- 执行 Ajax 请求
- 执行事件订阅

更新时

1. 新属性、setState、forceUpdate
2. getDerivedStateFromProps 根据属性设置 state
3. shouldComponentUpdate 组件判断是否重新渲染，默认返回 true 则往下执行，否则中断，正确使用这个特性可以提升程序性能，减少无用的渲染
4. render
5. getSnapshotBeforeUpdate 此时 DOM 已经渲染好了但还没更新到实际 DOM 上，返回任意值将作为下一个生命周期函数即 componentDidUpdate() 的第三个参数
6. render 更新 DOM
7. componentDidUpdate 组件完成更新

卸载时

componentWillUnmount

- 在这里应该取消网络请求
- 清除任何在 componentDidMount 环节创建的 DOM 元素
- 取消事件订阅

## 高阶组件

Fn(组件) = 高阶组件

高阶组件的作用：高阶组件实现了组件逻辑的重用

组件中的通用逻辑，提取出来，整合到高阶组件

注意：

- 高阶组件不会改变原始组件
- 高阶组件是对原始组件的能力加强