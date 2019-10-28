[redux 基础](https://github.com/tayiorbeii/egghead.io_redux_course_notes) 作者亲自录视频教，感觉不错，不过这个笔记不是作者的

[redux 进阶](https://github.com/tayiorbeii/egghead.io_idiomatic_redux_course_notes)

其实我们的很多需求 redux 还是做不成的，所以我们要给 redux 增强能力

哪里最适合增强 redux 的能力呢？

一般**在 ActionCreator 发出动作到 Dispatcher 里的时候**，中间件就是这么做的

## Redux 中间件

applyMiddleware(mw1, mw2, mw3)，先调用 mw1, 再调用 mw2, 再调用 mw3中间件调用 next() 将执行权交给下一个中间件

![Redux 中间件](https://i.loli.net/2019/01/10/5c37148a44b2c.png)

`({getState, dispatch}) => next => action => next(action)` 高阶函数柯里化

```js
// 中间件模板
function middleware({getState, dispatch}) { // 中间件生成
    return function  (next) { // 中间件
        return function (action) {
            // do something
            return next(action)
        }
    }
}
```



```js
// Redux 源码中 applyMiddleware 的实现
export default function applyMiddleware(...middlewares) {
    return createStore => (...args) => {
        const store = createStore(...args)
        let dispatch = () => {
            throw new Error('Dispatching while constructing your middleware is not allowed.' + 'Other middleware would not be applied to this dispatch.')
        }
        
        const middlewareAPI = {
            getState: store.getState,
            dispatch: (...args) => dispatch(..args)
        }
        const chain = middlewares.map(middleware => middleware(middlewareAPI))
        dispatch = compose(...chain)(store.dispatch)
        
        return {
            ...store,
            dispatch
        }
    }
}
```

例子：

```js
import { createStore, applyMiddleware } from 'redux'
import Reducer from './reducers'

// 定义 middleware
const authorMiddleware = ({getState}) => next => action => {
    action.author = 'chiehyang'
    return next(action)
}

// 定义 middleware
const loggerMiddleware = ({getState}) => next => action => {
    console.log('prev state: ', getState())
    console.log('action: ', action)
    const returnValue = next(action)
    console.log('next state: ', getState())
    return returnValue
}

export default createStore(Reducer, applyMiddleware(authorMiddleware, loggerMiddleware)) // 应用 middleaare，中间件在后面的就晚调用
```

## 分解 AJAX 操作

为什么要分解 AJAX？

- Redux 中的 Action 是同步的，接受到 Action 之后数据就会变化
- AJAX 是异步的，等待服务器返回数据后才会变化
- 所以，将异步的 AJAX 拆解为同步的状态变化过程

```jsx
componentDidMount() {
    const api = 'this.is.your.api/get'
    dispatch(requestStart(api))
    
    axios.get(api)
    .then(
        res => dispatch(requestSuccess(res.data))
    ).catch(
        res => dispatch(requestFailure(error))
    )
} // 这种写法不好，数据逻辑理论上应该在 ActionCreator 里面处理，react 组件只是处理一些展示相关的事情，所有的动作都是放在 action 里处理的，我们应该用中间件来解决这样的问题
```

### 改造方案（两种）

- 在 ActionCreator 中不再返回简单的 Action 对象，而是返回一个函数或者 Promise （使用 redux-thunk）
- 使用中间件来解析特殊类型的 Action 对象，自动拆解为同步的简单 Action（使用redux-promise）

这也衍生出两种解决方案，返回函数或者 Promise

```jsx
// actionCreator
import axios from 'axios'

const ActionTypes = {
    REQUEST_START: 'GET_CONFIG/REQUEST_START',
    REQUEST_SUCCESS: 'GET_CONFIG/REQUEST_SUCCESS',
    REQUEST_FAILURE: 'GET_CONFIG/REQUEST_FAILURE',
}

export { ActionTypes }

const requestStart = () => ({
    type: ActionTypes.REQUEST_START
})

const requestSuccess = (result) => ({
    type: ActionTypes.REQUEST_SUCCESS,
    payload: { result }
})
const requestFailure = (error) => ({
    type: ActionTypes.REQUEST_FAILURE,
    payload: { error }
})

const URL = '/getConfig'
export default {
    loadInitData: () => dispatch => { // action creator, 这里的参数其实还有 getState, extraArgument，但是没有用到就不写了
        dispatch(requestStart())
        axios.get(URL)
        .then(res => dispatch(requestSuccess(res.data)))
        .catch(error => dispatch(requestFailure(error)))
    }
}
```

```js
// redux thunk 源码
function createThunkMiddleware(extraArgument) {
    return ({ dispatch, getState }) => next => action => {
        if(typeof action === 'function') {
            return action(dispatch, getState, extraArgument) // 赋予函数 action 更强的能力，允许它使用 dispatch 和 getState
        }
        
        return next(action)
    }
}

const thunk = createThunkMiddleware()
think.withExtraArgument = createThunkMiddleware

export default thunk
```

![SIDE EFFECTS](https://s3.amazonaws.com/media-p.slid.es/uploads/364812/images/2484716/ARCH-Redux2-extended-real.gif)

![SIDE EFFECTS - LESS BOILERPLATE](https://s3.amazonaws.com/media-p.slid.es/uploads/364812/images/2484790/ARCH-Redux2-extended-real-declerative.gif)

easy: redux-thunk、redux-promise

complex: redux-saga