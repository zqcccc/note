## Flux

一种使得 React 应用数据单向流动的 **架构**

### 以往的数据架构

MVC 架构：

Action => Controller => Model => View

数据增长后，

Model 和 View 疯狂增长，他们的关系变得十分复杂，维护几乎不可能

React 中的数据流：

React 的父子组件，兄弟组件之间都可以相互传递数据，数据流向复杂不清晰，也是难以开发维护

### 解决方案

两者结合，更加受限的**单向**流动

数据总是从源节点流向控制中心，再流向目标节点

![flux 数据流动](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016011503.png)

React 作为视图层 View，Flux 中的 Store 则作为数据中心 M， Flux 中的 Dispatcer 则可作为一个控制中心 C，负责用户行为的响应和 Modal 层的改变。React 不一定需要绑定 Flux，但在复杂度高的项目中使用 Flux 架构，可以让项目中的数据流更加清晰可预测，利于项目的维护和迭代。可以说项目复杂度越高，越适合引入 Flux 架构。Flux 约定我们视图层不直接修改组件的状态，应用的状态应当放到 Store 中进行管理，组件状态的修改需要触发 Action 

## Flux 是一个架构而不是框架

- 中心化控制，改变通过 action 发出，由 dispatcher 来分配
- view 保持简洁，只需要关心传进的数据
- 数据始终保持单向流动

### Dispatcher -- 控制中心

- 保持单例
- 接受 action 派发给 store

示例：

1. 用户创建了一个 todo，点击了“添加”按钮
2. view 捕获了这个事件，dispatcher 将这个“ADD_TODO” action 抛出
3. 每一个 store 收到了这个 action

先创建 dispatcher

```js
// dispatcher.js
import { Dispatcher } from 'flux'
export default new Dispatcher()
```

派发 action

```js
// actions.js，在点击事件的处理函数当中派发 action
Dispatcher.dispatch({
    type: ActionType.ADD_TODO,
    payload: {
        text
    }
})
```

连接 Dispacher 和 Store

```js
// store.js 简化版
import { ReduceStore } from 'flux/utils'
import Dispatcher from './dispatcher'

class Store extends ReduceStore {
    constructor() {
        super(Dispatcher) // 在 Store 的构造函数当中绑定 Dispatcher，绑定 Store 和 Dispatcher
    }
    
    reduce(state, action) {
        // ...
    }
}

export default new Store()
```

### Store -- 用于存储应用中的数据

- 用于接受 actions
- 只可以被 action 修改
- 允许多个实例

示例：

1. store 接受了“ADD_TODO”这个 action
2. store 中判断这是一个需要处理的 action，进入处理逻辑
3. store 更新自己的 state

```js
// store.js
import { ReduceStore } from 'flux/utils'
import { ActionTypes } from './actions'
import Dispatcher from './dispatcher'

class Store extends ReduceStore {
  constructor() {
    super(Dispatcher) // 在 Store 的构造函数当中绑定 Dispatcher
  }
  
  getInitialState() { // Store 中的数据保存在 state 中，这里定义初始值
    return {
      list: [],
      nextId: 1
    }
  }
  
  reduce(state, action) { // 收到 action 都会触发 reduce 方法
    switch (action.type) { // 根据 Action 的类型来处理数据
      case ActionTypes.ADD_TODO:
        {
          const { payload } = action
          const { list = [] } = state
          const nextList = list.concat()
          const nextList = list.concat([{
            id: state.nextId,
            text: payload.text
          }])
          return Object.assign({}, state, { // 返回新的 state，Store 里的值就更新了
            list: nextList,
            nextId: state.nextId + 1
          })
        }
      default:
        return state
    }
  }
}

export default new Store()
```

### Action -- 发生的事件

- 语义化
- 准确描述的对象

示例：

用户点击了“添加”，导致了一个“ADD_TODO” action 被派发

```js
// actions.js
import Dispatcher from './dispatcher'

const ActionTypes = { // 为了集中管理 Actions 类型
  ADD_TODO: 'ADD_TODO'
}

export {
  ActionTypes
}

export default { // 集中管理 Actions
  addTodo(text) {
    Dispatcher.dispatcher({ // 这里面的对象才是真正意义上的 Action  
      type: ActionTypes.ADD_TODO,
      payload: {
        text
      }
    })
  }
}
```

### View -- 展现 store 中的数据形态

- 可以是任何的前端框架（一般是 React）
- 和 Store 共享数据

示例：

1. view 订阅了 TodoStore，渲染了 TodoStore 的内容
2. 当用户按下了“添加”之后，Dispatcher 将发送这个 action
3. 所有的 store 收到了这个 action
4. TodoStore 处理了这个 action，更改了相应的数据
5. view 也进行了更新，做出响应渲染上的改变

```jsx
// index.jsx
import React from 'react'
import TodoStore from './store'
import Actions from './actions'

export default class TodoList extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      todolist: TodoStore.getInitialState().list
    }
    this.$input = React.createRef()
    this.onStoreChange = this.onStoreChange.bind(this)
  }

  componentDidMount() {
    this.removeListener = TodoStore.addListener(this.onStoreChange) // 监听 Store 的变化，并返回一个 token，是一个函数
  }
  
  componentWillUnmount() {
    this.removeListener() // 调用上面的 token，就后释放 listener
  }

  onStoreChange() { // Store 更新后，同步更新组件 state
    this.setState({
      todolist: TodoStore.getState().list
    })
  }

  addItem() {
    const text = this.$input.current.value
    Actions.addTodo(text) // 在事件监听函数中，调用封装好的 Action 生成方法
  }

  render() {
    return (
      <div>
        <ul>
          {
            this.state.todolist && this.state.todolist.map(item => (
              <li key={item.id}>
                <span>{item.text}</span>
              </li>
            ))
          }
        </ul>
        <input ref={this.$input} />
        <button onClick={this.addItem.bind(this)}>添加</button>
      </div>
    )
  }
}
```

