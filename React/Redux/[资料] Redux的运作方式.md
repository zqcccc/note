# Redux 的运作方式

## 核心模块

Redux 作为一个状态管理框架，包含三大核心模块：

- Action：视图层发出的通知，通知 Store 执行数据更新，同时它也作为 Store 唯一的数据来源。
- Store：Redux 中的数据容器，且 Store 在 Redux 中是唯一的。
- Reducer：Reducer 指定了 Store 应当如何响应 Action 的更新。

除了以上三个模块，还有一个 View 层，在 Redux 中一般指 React 视图层。Redux 的数据在这四个模块之间的流转严格遵循**单向数据流**的原则。

## Action

在 Redux 中，**state 是不可变的**，数据的更新需要通过分发 Action 来实现。Action 本质是一个 JavaScript 对象，type 是其必要的一个属性，代表 Action 的名称，该属性的缺失将导致 Action 不能被识别而无法更新数据。

我们一般通过 action creator 函数来生成 Action：

```
const editTitle = function(title) {
  return {
    type: 'EDIT_TITLE',
    title,
  }
}
// 使用 editTitle 方法生成一个 action
const action = editTitle('hello Redux');
```

## Reducer

Reducer 是 Redux 中唯一能识别 Action 的模块。它接收 Store 当前的状态和分发的 Action，并返回一个新的 State 给到 Store。需要注意，**Reducer 是一个纯函数**，函数中不能修改 State，而是创建一个新的 State 对象，而修改的状态也将被合并到该对象。

Reducer.js

```js
// 默认状态
const initialState = {
  title: '',
  content: ''
};
export default function(state = initialState, action) {
  switch (action.type) {
    case 'EDIT_TITLE':
      return Object.assign({}, state, {
        title: action.title,
      });
    case 'EDIT_CONTENT':
      return Object.assign({}, state, {
        content: action.content,
      });
    default: 
      return state;
  }
}
```

可以看到，Reducer 根据 Action 的 type 做不同的数据更新。当 Action 的 type 无法识别时，Reducer 将返回原先的 State。
同时，Reducer 中还设置了一个初始的状态 initialState。在应用初始化的时候，Store 传过来的 State 有可能是 undefined，这个时候 Reducer 则需要返回其设置的初始状态 initialState。每一个 Reducer 都需要设置一个初始状态。

## Store

Store 存储了 Redux 应用中的所有状态，并提供了相关访问 Store 当前状态的方法，同时 Store 也作为 Action 和 Reducer 之间的纽带，将两者联结在一起。

### 创建 Store：createStore 方法

Redux 提供了一个 createStore 方法，用于创建 Store。
该方法接收三个参数：

- 第一个参数接收一个 Reducer。
- 第二个参数是一个对象，它为 Store 指定了应用的初始状态，当项目中涉及服务器端的 Redux 时，这个参数可以帮助我们同步服务器端和客户端的数据结构。
- 第三个参数是一个函数，用于指定 Redux 中间件。

需要注意，当需要指定中间件且不需要指定初始状态的时候，第三个参数可提到第二个参数的位置，createStore 方法会根据第二个参数的类型将其识别为初始状态或者中间件函数。

创建一个 Store 时，我们一般只需要传递第一个参数：

```
import { createStore } from 'redux'
import Reduces from './reducers'
let store = createStore(Reduces)
```

### 数据的初始化

Store 状态的初始化可分为两个阶段。第一阶段正是通过 createStore 方法所接收的第二个参数实现的初始化。这个阶段的初始化常见于服务端的 Redux 渲染时，服务端的渲染会把请求到的状态注入到 `window.__PRELOADED_STATE__`，客户端可以通过该全局变量得到初始的状态对象，并将其作为 createStore 的第二个参数传入，进而完成初次状态的初始化。

这个场景下初始化的状态一般都会包含首屏展示所需的数据，这也正是使用服务端渲染的原因：用于加快首屏的展示。但是在 Reducer 被切分成多个模块的时候，参数实现的初始化难免会有遗漏，这个时候就需要我们进行第二次的数据初始化。

我们来看一段 createStore 方法的实现源码：

```
export const ActionTypes = {
  INIT: '@@redux/INIT'
}

export default function createStore(reducer, preloadedState, enhancer) {
  ...
  // When a store is created, an "INIT" action is dispatched so that every
  // reducer returns their initial state. This effectively populates
  // the initial state tree.
  dispatch({ type: ActionTypes.INIT })

  return {
    ....
  }
}
```

在 createStore 的实现源码中可以看到，它在最后一步调用了内置的 dispatch 方法，分发了一个 type 为 ActionTypes.INIT 的 action 对象。
我们知道，Reducer 根据 action 的 type 做不同的数据处理。如果 action 中的 type 与其不相匹配，则需要执行 'default' 操作：返回原先的 State。
createStore 方法正是利用了 Reducer 的这个特性，通过分发一个不会被 Reducer 所识别的 action，让 Reducer 返回各自的初始状态，进而完成了 Store 数据的第二次初始化。

我们总结一下 Store 的初始化流程：

![图片](http://p.qpic.cn/qqconadmin/0/79e57ff512bf4a92a0833520c2645542/0)

### 数据的更新：dispatch 方法

Store 内置的 dispatch 方法，充当了 Action 与 Reducer 之间的桥梁。通过 dispatch 方法，实现了 Action 从 Store 层到 Reducer 层的转发。我们可以在 View 层通过该方法分发 Action，进而实现 Store 层数据的更新。

我们看 dispatch 的源码是如何实现 Action 的分发：

```
function dispatch(action) {
  ...
  if (isDispatching) {
    throw new Error('Reducers may not dispatch actions.')
  }

  try {
    isDispatching = true
    currentState = currentReducer(currentState, action)
  } finally {
    isDispatching = false
  }

  const listeners = currentListeners = nextListeners
  for (let i = 0; i < listeners.length; i++) {
    const listener = listeners[i]
    listener()
  }

  return action
}
```

源码中的 currentReducer 正是我们调用 createStore 方法时传入的 Reducer，currentState 对应 Store 当前的状态。也就是说，dispatch 方法直接调用了传入的 Reducer 函数，并向其转发了 Store 当前的状态和被分发的 action。同时的，Reducer 函数处理返回的结果也将直接被保存到 currentState 中。
需要注意，Redux 的数据更新只支持同步处理，如果需要执行异步处理，则需要引入 react-thunk 中间件。

### 数据的监听：subscribe

数据从 Store 层到 View 层的流向，正是依赖于 Store 中的内置 subscribe 方法。该方法接收一个回调函数，并返回一个取消监听的函数。

添加 Store 的数据监听：

```
import store from './store';
const unSubscribe = store.subscribe(() =>
  // 通过 getState 方法获取 Store 当前的状态
  let state = store.getState();
  // 更新视图
  ...
)
```

每一次数据的更新，都会执行 subscribe 中传入的回调函数。我们可以在 View 层添加这个回调函数，回调函数中则通过 store.getState() 获取 Store 当前的数据，进而执行视图层的更新。

#### 数据监听原理解析

Store 中执行监听回调的地方在 dispatch 方法内部。也就是说，在执行 dispatch 方法的时候，我们添加的监听回调都会被执行一遍。这一点在 dispatch 方法的源码中也有体现：

```
function dispatch(action) {
  ...
  const listeners = currentListeners = nextListeners
  for (let i = 0; i < listeners.length; i++) {
    const listener = listeners[i]
    listener()
  }
  return action
}
```

可以看到，Store 内部有一个存储监听函数的数组 nextListeners，每次执行 dispatch 方法时都会遍历调用该数组中所有的监听函数。
我们新增的监听回调是作为 subscrib 函数的参数传入的，所以我们可以推断，subscribe 函数的作用正是帮助我们把新增的监听回调添加到 nextListeners 数组中。

我们的推断可以在 subscribe 源码中得到验证：

```
function subscribe(listener) {
  if (typeof listener !== 'function') {
    throw new Error('Expected listener to be a function.')
  }

  let isSubscribed = true

  ensureCanMutateNextListeners()
  // 把新增的监听回调添加到 nextListeners 数组中
  nextListeners.push(listener)

  return function unsubscribe() {
    if (!isSubscribed) {
      return
    }

    isSubscribed = false

    ensureCanMutateNextListeners()
    const index = nextListeners.indexOf(listener)
    // 把新增的监听回调从 nextListeners 数组中移除
    nextListeners.splice(index, 1)
  }
}
```

nextListeners 是一个数组，所以我们可以在多个视图层添加 Store 的监听回调。同时，subscribe 方法会返回一个取消监听的函数。有的时候我们希望不再接收第一次以后的数据更新，这个时候我们可以使用该方法移除监听。