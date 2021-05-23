入门：https://codesandbox.io/s/react-hooks-demo1-cqdzt

dan 的完整指南：https://overreacted.io/zh-hans/a-complete-guide-to-useeffect

useState 不能存在于条件语句中，hooks 的状态记录是根据顺序去记录的，所以如果在条件判断的语句中去使用 useState 声明变量是不对的，这是 hooks 特殊的限制

用 useState 得到的 `[state, setState]` 的 `setState` 也接受一个函数，函数的参数就是 state 的旧值，函数返回的值就会设置成新的值

`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`

## 类组件的不足

1. 缺少逻辑复用机制，为了复用逻辑增加无实际渲染效果的组件，增加了组件层级 显示十分臃肿 增加了调试的难度以及运行效率的降低
2. 类组件经常会变得很复杂难以维护，将一组相干的业务逻辑拆分到了多个生命周期函数中，在一个生命周期函数内存在多个不相干的业务逻辑
3. 类成员方法不能保证this指向的正确性

Hooks 要解决的问题就是类组件的不足

## React Hooks

Hooks 意为钩子, React Hooks 就是一堆钩子函数, React 通过这些钩子函数对函数型组件进行增强, 不同的钩子函数提供了不同的功能.

```ts
useState() useEffects() useReducer() useRef() useCallback() useContext() useMemo()
```

以前函数式组件是不能保存状态的，现在通过闭包的方式来保存状态了

### useState()

1. 接收唯一的参数即状态初始值. 初始值可以是任意数据类型.
2. 返回值为数组. 数组中存储状态值和更改状态值的方法. 方法名称约定以set开头, 后面加上状态名称.
3. 方法可以被调用多次. 用以保存不同状态值.
4. 参数可以是一个函数, 函数返回什么, 初始状态就是什么, 函数只会被调用一次, 用在初始值是动态值的情况.

设置状态值方法的参数可以是一个值也可以是一个函数

设置状态值方法的方法本身是异步的

### useReducer()

useReducer 是另一种让函数组件保存状态的方式，参考 redux 中的 reducer

### useContext()

useContext() 在跨组件层级获取数据时简化获取数据的代码

```tsx
import React, { createContext, useContext } from 'react'

const countContext = createContext()
function App () {
  return <countContext.Provider>
    <Foo1 />
  	<Foo2 />
  </countContext.Provider>
}

function Foo1 () {
  return <countContext.Consumer> // 这样写难免有点麻烦
    {
      value => <div>{value}</div>
    }
  </countContext.Consumer>
}

function Foo2 () {
  const value = useContext(countContext)
  return <div>{value}</div>
}
```

### useEffect() 

`useEffect()` 让函数型组件拥有处理副作用的能力. 类似生命周期函数.

可以把 useEffect 看做 componentDidMount, componentDidUpdate 和 componentWillUnmount 这三个函数的组合.

`useEffect(() => {}) => componentDidMount, componentDidUpdate`

`useEffect(() => {}, []) => componentDidMount`

`useEffect(() => () => {}) => componentWillUnMount`

其实也没必要像上面那样去认识 useEffect，**只有指定数据发生变化时触发effect**，也就是 useEffect 传递的第二个参数里的依赖发生了变化才会触发 effect

#### useEffect 解决的问题

1. 按照用途将代码进行分类 (将一组相干的业务逻辑归置到了同一个副作用函数中) 
2. 简化重复代码, 使组件内部代码更加清晰

#### useEffect 结合异步函数

useEffect中的参数函数不能是异步函数, 因为useEffect函数要返回清理资源的函数, 如果是异步函数就变成了返回Promise

```tsx
useEffect(() => {
	(async () => {
		await axios.get()
	})()
}, [])
```

### useMemo()

useMemo 的行为类似Vue中的计算属性, 可以监测某个值的变化, 根据变化值计算新值.

useMemo 会缓存计算结果. 如果监测值没有发生变化, 即使组件重新渲染, 也不会重新计算. 此行为可以有助于避免在每个渲染上进行昂贵的计算.

```tsx
import { useMemo } from 'react'

const result = useMemo(() => {
  return 123
}, [count])
```

### memo 方法

做性能优化, 如果本组件中的数据没有发生变化, 阻止组件更新. 类似类组件中的 PureComponent 和 shouldComponentUpdate

```tsx
import React, { memo } from 'react'

function Counter () {
  return <div></div>
}

export default memo(Counter)
```

使用这个组件，props 不变的情况下就不会更新这个组件，无论父组件是不是重新渲染

### useCallback()

性能优化, 缓存函数, 使组件重新渲染时得到相同的函数实例.

### useRef()

1.  获取DOM元素对象
2. 保存数据 (跨组件周期)，即使组件重新渲染, 保存的数据仍然还在. 保存的数据被更改不会触发组件重新渲染.

### 自定义 **Hook**

自定义 Hook 是标准的封装和共享逻辑的方式. 

自定义 Hook 是一个函数, 其名称以 use 开头. 

自定义 Hook 其实就是逻辑和内置 Hook 的组合.

```tsx
function useUpdateInput (initialValue) {
  const [value, setValue] = useState(initialValue)
  return {
    value,
    onChange: event => setValue(event.target.value)
  }
}

function App() {
  const userNameInput = useUpdateInput('')
  const passwordInput = useUpdateInput('')
  const submitForm = event => {
    event.preventDefault()
    console.log(userNameInput.value)
    console.log(passwordInput.value)
  }
  return <form>
    <input type="text" name="username" {...userNameInput}/>
    <input type="password" name="password" {...passwordInput}/>
    <input type="submit" />
  </form>
}
```

## useState 实现原理

```tsx
import React from 'react'
import ReactDOM from 'react-dom'

let state = []
let setters = []
let stateIndex = 0

function createSetter(index) {
  return function (newState) {
    state[index] = newState
    render()
  }
}
function useState(initialValue) {
	state[stateIndex] = state[stateIndex] ? state[stateIndex] : initialState
  setters.push(createSetter(stateIndex))
  let value = state[stateIndex]
  let setter = setters[stateIndex]
  stateIndex++
  return [state, setState]
}

function render() {
  stateIndex = 0
  ReactDOM.render(<App />, document.getElementById('root'))
}

function App() {
  const [count, setCount] = useState(0);
  return <div>
    {count}
    <button onClick={() => setCount(++count)}>+1</button>
    <button onClick={() => setCount(--count)}>-1</button>
  </div>;
}

export default App
```

## useEffect 实现原理

```tsx

// 上一次的依赖值
let prevDepsAry = [];
let effectIndex = 0;

function useEffect(callback, depsAry) {
  // 判断callback是不是函数
  if (Object.prototype.toString.call(callback) !== '[object Function]') throw new Error('useEffect函数的第一个参数必须是函数');
  // 判断depsAry有没有被传递
  if (typeof depsAry === 'undefined') {
    // 没有传递
    callback();
  } else {
    // 判断depsAry是不是数组
    if (Object.prototype.toString.call(depsAry) !== '[object Array]') throw new Error('useEffect函数的第二个参数必须是数组');
    // 获取上一次的状态值
    let prevDeps = prevDepsAry[effectIndex];
    // 将当前的依赖值和上一次的依赖值做对比 如果有变化 调用callback
    let hasChanged = prevDeps ? depsAry.every((dep, index) => dep === prevDeps[index]) === false : true;
    // 判断值是否有变化
    if (hasChanged) {
      callback();
    }
    // 同步依赖值
    prevDepsAry[effectIndex] = depsAry;
    effectIndex++;
  }
}
```

因为刚开始 `prevDepsAry` 是空值，所以某个依赖项为空数组的 `effect` 第一次执行的时候，之前的依赖是 `undefined`，这种情况就会执行 `callback` 函数

## useReducer 实现原理

可以说是 `useState` 的增强版本，用的是 `redux` 里 `reducer` 的思想

```tsx
function useReducer (reducer, initialState) {
	const [state, setState] = useState(initialState)
  function dispatch(action) {
    const newState =  reducer(state, action)
    setState(newState)
  }
  return [state, diaptch]
}
```

