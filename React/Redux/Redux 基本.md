## flux

 - 一种架构模式，而不是一种框架
- 数据单向流动

## Redux 简介

[官网 getting-started](https://redux.js.org/introduction/getting-started)

[官方例子及源码](https://redux.js.org/introduction/getting-started#examples)

- Flux 架构的一个实现
- 一个可预测的状态容器
- Flux 架构与函数式编程思想的结合

store 只有一个，dispatcher 也只有一个所以这两个就关联起来了，store 里面的 reducers 是一种 flux 的实现

### Redux 的基本原则

- 唯一数据源，Have a single source of truth: The state of your whole application is stored in an object tree within a single Redux store.

- 保持状态（state）只读，State is read-only: The only way to change the state is to emit an action, an object describing what happened.

- 数据改变只能通过纯函数来完成（reducer）To specify how the state tree is transformed by actions, you write pure reducers.

### 使用 Redux 的时候

- 存在本地存储或从服务器带来的初始数据
- 需要记录用户操作、实现撤销功能或在不同状态间跳转
- 组件之间状态相互依赖严重，组件数据源复杂

## 纯函数

定义：
1. 给定相同的输入，总能获得相同的输出
2. 函数过程中不会产生副作用
3. 不依赖外部状态（只从入参读取变量）

```js
// 以下函数都不是纯函数
function  getNumber()  {
  return  Math.random()
}

function  getLastElement(array)  {
  return  array.pop()
}

function  getTime()  {
  return  new  Date().toLocaleDateString()
}
```

## 基本元素

![redux](https://s3.amazonaws.com/media-p.slid.es/uploads/364812/images/2484552/ARCH-Redux2-real.gif)

### Action

- store 的唯一信息来源
- store.dispatch(action)

action 和 actionCreator 很容易弄混，要注意区分

```json
{
    type: ActionTypes.ADD_TODO,
    payload: {
        text
    }
} // action
```

```js
addTodo: (text) => ({
    type: ActionType.ADD_TODO,
    payload: {
        text
    }
}) // actionCreator
```



### Reducer

- 处理 state 变化的纯函数
- **(state, action) => newState**
- immutable

```js
export default (state, action) => { // 纯函数
  switch (action.type) { // 根据 action 分发
    case ActionTypes.ADD_TODO:
    {
      const { payload } = action
      const { list = [] } = state
      const nextList = list.concat([{ // immutable
        id: state.nextId,
        text: payload.text
      }])
      return Object.assign({}, state, {
        list: nextList,
        nextId: state.nextId + 1
      })
    }
    case ActionTypes.DELETE_TODO:
    {
      const { payload } = action
      const { list = [] } = state
      const nextList = list.filter(item => (item.id !== payload.id))
      return Object.assign({}, state, {
        list: nextList
      })
    }
    default:
    return state; // 未匹配处理，遇到未知的 action 时，一定要返回旧的 state。
  }
}
```

[`Object.assign()`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 是 ES6 特性，但多数浏览器并不支持。你要么使用 polyfill，[Babel 插件](https://github.com/babel-plugins/babel-plugin-object-assign)，或者使用其它库如 [`_.assign()`](https://lodash.com/docs#assign) 提供的帮助方法。

**注意每个 reducer 可以只负责管理全局 state 中它负责的一部分。每个 reducer 的 state 参数都不同，分别对应它管理的那部分 state 数据。** 

### Store

保存现在状态的数据

```js
import {createStore} from 'redux'
import Reducer from './reducer'

const initialState = { list: [], nextId: 0 }
export default createStore(Reducer, initialState) // 创建 store 方法

...

TodoStore.dispatch(Actions.addTodo(text)) // 派发事件方法

...

// index.jsx
onStoreChange() {
    this.setState({
        todolist: TodoStore.getState().list // 获的当前状态
    })
}

...

TodoStore.subscribe(this.onStoreChange) // 订阅 store 方法
```

```js
// 一个 createStore 的基本实现
const counter = (state = 0, action) => {
  switch(action.type){
      case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;}
}
  
// ****核心****
const createStore = (reducer) => {
  let state;
  let listeners = [];
  
  const getState = () => state;
  
  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };
  
  const subscribe = (listener) => {
    listeners.push(listener)
    return () => {
      listeners = listeners.filter(l => l !== listener);
    };
  };
  
  dispatch({});
  
  return { getState, dispatch, subscribe };
};
// ****核心****

const store = createStore(counter)

const render = () => {
  document.body.innerText = store.getState()
}

store.subscribe(render)
render()

document.addEventListener('click',()=> {
  store.dispatch({type: 'INCREMENT'})
})
```



Redux 由三个核心元素组成：Action、Store、Reducer，React 则作为应用中的视图层 View。
一个完整 Redux 的数据流是：

1. View 通过使用 Store 提供的 dispatch 方法分发 Action， 以发起状态更新的请求。
2. Store 接收 Action，并将该 Action 和 Store 当前的状态 State 作为参数传递给 Reducer。
3. Reducer 根据 Store 提供的 State 和 Action，执行数据更新，并将新状态返回到 Store 。
4. View 监听到 Store 的数据变化，执行对应的视图更新。



## 拓展

combineReduces 合并多个 reducer

```js
const combineReducers = (reducers) => {
    return (state = {}, action) => {
        return Object.keys(reducers).reduce((nextState, key) => {
            nextState[key] = reducers[key](state[key], action);
            return nextState;
        }, {})
    }
}
```

