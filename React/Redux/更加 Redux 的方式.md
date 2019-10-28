组件做了什么？

- 和 Store 保持同步：监听 Store 的变化，从 Store 中获取数据
- 渲染界面：根据 props 和 state 来渲染界面

## 容器组件和傻瓜组件

- 和 Store 保持同步的是容器组件 ContainerComponent
- 渲染界面的是傻瓜组件 DumbComponent

这两个组件通过 props 来沟通，大体的结构是容器组件和 Store 沟通，以 props 的形式将需要展示的数据传递给傻瓜组件，傻瓜组件只要专心的渲染 UI 就行了，这样就把数据展示和业务逻辑分开了，更换哪一个都变得很简单

|                | 展示组件                   | 容器组件                           |
| -------------- | -------------------------- | ---------------------------------- |
| 作用           | 描述如何展现（骨架、样式） | 描述如何运行（数据获取、状态更新） |
| 直接使用 Redux | 否                         | 是                                 |
| 数据来源       | props                      | 监听 Redux state                   |
| 数据修改       | 从 props 调用回调函数      | 向 Redux 派发 actions              |
| 调用方式       | 手动                       | 通常由 React Redux 生成            |

## 处理更复杂的数据

### 存在的问题

- 容器组件和傻瓜组件强耦合，代码冗余
  - 多组容器组件和傻瓜组件之间交互的逻辑是一样的，
- Redux 应用只有一个 Store，处处都要 import

### 通过 props 传递什么？

- Store 中的 state，容器向核心组件传递数据
- Store 中的 dispatch 方法，核心组件向容器组件发起行为

这样数据和方法都传递到傻瓜组件里面去了，傻瓜组件就从 this.props 里面读取要用的数据和方法

### 高阶组件

高阶组件正好解决了这个强耦合的问题，可以把容器组件向傻瓜组件传递的属性和方法抽象成高阶组件

```js
function connect(mapStateToProps, mapDispatchToProps) { 
  return function connectHOC(Component) { // 高阶组件
    return class Connect extends React.Component {
      constructor(props) {
        super(props)
        this.state = Object.assign({}, 
          mapStateToProps(store.getState()),
          mapDispatchToProps(store.dispatch)
          ) // 将 state, dispatch 传出
          this.onStoreChange = this.onStoreChange.bind(this)
      }

      componentDidMount() {
        store.subscribe(this.onStoreChange)
      }

      
      componentWillMount() {
        store.unsubscribe(this.onStoreChange)
      }
      
      onStoreChange() {
        const state = store.getState()
        this.setState(mapStateToProps(state))
      }

      render() {
        return <Component {...this.props} {...this.state} />
      }
    }
  }
}

function mapStateToProps(state) {
  const { todos } = state // 获取 store 里的 todos
  return {
    todos: todos.list // 映射 props 属性
  } 
}

function mapDispatchToProps(dispatch) {
  return {
    toggleTodo: id => dispatch(TodoActions.toggleTodo(id)),
    deleteTodo: id => dispatch(TodoActions.deleteTodo(id)) // 映射 dispatch 方法
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(TodoList) // connect 高阶组件
```

### 设置全局可访问的 Store

- 利用 props 层层传递（不可取，太麻烦且冗余）
- 利用 Context

设置一个可全局访问的 Context，官方的视频教程也不推荐 context，因为它总是变动

- React 在16.3版本之后提供了新的 Context API，我们需要使用旧的 API
- Context 需要在最顶层的组件提供，这样所有下层组件才能访问
- 在最外层包裹一个层组件，传入 Store 作为 props，并将其赋值给 Context
- 需要使用 Store 的子组件通过 Context 访问

```js
class Provider extends React.Component {
  getChildContext() {
    return {
      store: this.props.store
    }
  }

  render() {
    return this.props.children
  }
}

Provider.childContextTypes = {
  store: PropTypes.object
}

ReactDOM.render(
  <Provider store={Store}>
    <App />
  </Provider>,
  document.querySelector('#app')
)

function connect(mapStateToProps, mapDispatchToProps) {
  return function connectHOC(Component) {
    class Connect extends React.Component {
      constructor(props, context) {
        super(props,context)
        this.state = Object.assign({}, 
          mapStateToProps(store.getState()),
          mapDispatchToProps(store.dispatch)
          )
          this.onStoreChange = this.onStoreChange.bind(this)
      }

      componentDidMount() {
        this.context.store.subscribe(this.onStoreChange)
      }

      
      componentWillMount() {
        this.context.store.unsubscribe(this.onStoreChange)
      }
      
      onStoreChange() {
        const state = store.getState()
        this.setState(mapStateToProps(state))
      }

      render() {
        return <Component {...this.props} {...this.state} />
      }
    }

    Connect.contexstTypes = {
      store: PropTypes.Object
    }
    return Connect
  }
}
```

上面的代码当然不用每次都写一遍，这就是 react-redux

![react-redux](https://i.loli.net/2018/12/21/5c1ced6928902.png)

### 使用 react-redux

```js
import { Provider, connect } from "react-redux";

function mapStateToProps(state) { // 映射 props 属性
    const { todos } = state
    return {
        todos: todos.list
    }
}
function mapDispatchToProps(dispatch) { // 映射 dispatch 方法
    return {
        toggleTodo: id => dispatch(TodoActions.toggleTodo(id)),
        deleteTodo: id => dispatch(TodoActions.deleteTodo(id))
    }
}

export default connect(mapStateToProps, mapDispatchToProps)(TodoList) // connect 高阶组件
```

## 

