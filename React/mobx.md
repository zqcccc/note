# mobx6

不同版本的 mobx 的使用方式可能不一样，下面讲的都是 mobx6

[官方文档](https://mobx.js.org/README.html)

mobx 也是不和 React 耦合的，可用于其他框架

mobx 的使用就是在 store 类定义里面，引入 mobx 让属性变成 observable object，方法变成 action

mobx 严格模式下是只能在 action 中改变 state 的

## 直接使用

### makeObservable

从 mobx 中引入 makeObservable

在 class 的 constructor 里用 makeObservable 去让 this 对象变成可 observable

runInAction 接受一个回调，这个回调会被当成 action 执行，一般可以在这里写一些临时需要去改变 state 的方法

```ts
import TodoStore from "./TodoStore"
import {
  action,
  computed,
  makeObservable,
  observable,
  runInAction
} from "mobx"

import axios from "axios"

class TodoListStore {
  todos = []
  filter = "all"
  constructor(todos) {
    if (todos) this.todos = todos
    makeObservable(this, { // 这里就让 new 出来的对象变成了响应式的
      todos: observable,
      createTodo: action,
      removeTodo: action,
      unCompletedTodoCount: computed,
      filter: observable,
      changeFilter: action,
      filterTodos: computed
    })
    this.loadTodos()
  }
  get unCompletedTodoCount() {
    return this.todos.filter(todo => !todo.completed).length
  }
  get filterTodos() {
    switch (this.filter) {
      case "all":
        return this.todos
      case "active":
        return this.todos.filter(todo => !todo.completed)
      case "completed":
        return this.todos.filter(todo => todo.completed)
      default:
        return this.todos
    }
  }
  changeFilter(filter) {
    this.filter = filter
  }
  createTodo(title) {
    this.todos.push(new TodoStore(title))
  }
  removeTodo(id) {
    const index = this.todos.findIndex(todo => todo.id === id)
    this.todos.splice(index, 1)
  }
  async loadTodos() {
    let todos = await axios
      .get("http://localhost:3005/todos")
      .then(response => response.data)
    runInAction(() => {
      todos.forEach(todo => {
        this.todos.push(new TodoStore(todo.title))
      })
    })
  }
}

export default TodoListStore
```



如果只用 react hooks 的话，使用 mobx 下的 **mobx-react-lite** 即可，mobx 已经集成了和 react 一起工作的内容了

就用 `react createContext` 写 `Provider` 

```tsx
import CounterStore from "./Counter/CounterStore"
import TodoListStore from "./Todos/TodoListStore"
import { createContext, useContext } from "react"

class RootStore {
  constructor() {
    this.counterStore = new CounterStore()
    this.todoListStore = new TodoListStore()
  }
}

const RootStoreContext = createContext()

const RootStoreProvider = ({ store, children }) => {
  return (
    <RootStoreContext.Provider value={store}>
      {children}
    </RootStoreContext.Provider>
  )
}

const useRootStore = () => {
  return useContext(RootStoreContext)
}

export { RootStore, RootStoreProvider, useRootStore }

```

再到 app 根组件外面包一边，然后就可以在任意层级的子组件里面使用了

```tsx
import Counter from "./components/Counter/Counter"
import TodoListView from "./components/Todos/TodoListView"
import { RootStore, RootStoreProvider } from "./stores/RootStore"

const rootStore = new RootStore()

function App() {
  return (
    <RootStoreProvider store={rootStore}>
      <TodoListView />
      <Counter />
    </RootStoreProvider>
  )
}
```

在函数式组件里使用，直接就可以拿到 store

```ts
const { counterStore } = useRootStore()
```



## 计算属性

computed 的属性得是一个 getter

### 计算待办事项数量

待办事项数量属于派生状态，即该状态依赖现有状态 (todos) 生成。

派生状态可以使用计算值实现，当依赖状态发生变化后，计算值自动更新。

```react
// TodoListStore.js
import { computed } from "mobx"

class TodoListStore {
  constructor() {
    makeObservable(this, {
      unCompletedTodoCount: computed
    })
  }
  get unCompletedTodoCount() {
    return this.todos.filter(todo => !todo.completed).length
  }
}
// TodoFooter.js
import { useTodoListStore } from "../../stores/Todos/TodoListStore"
import { observer } from "mobx-react-lite"

function TodoFooter() {
  const todoListStore = useTodoListStore()
  return <strong>{todoListStore.unCompletedTodoCount}</strong> item left
}

export default observer(TodoFooter)
```

注意：计算值是被缓存的。

```react
get unCompletedTodoCount() {
  console.log("unCompletedTodoCount")
  return this.todos.filter(todo => !todo.completed).length
}
{todoListStore.unCompletedTodoCount}
{todoListStore.unCompletedTodoCount}
{todoListStore.unCompletedTodoCount}
// 计算属性被调用多次, 但是方法内部的console.log 只会输出一次, 说明计算属性是被缓存的.
```



## 数据监测

### autorun 方法

**这个方法的使用只需要初始化执行一次就可以了**

1. 监控数据变化执行副作用，接收一个函数作为参数，参数函数用来执行副作用，当参数函数内部使用的 observable state, computed 发生变化时函数会运行，初始运行 autorun 方法时参数函数也会运行一次。

   ```react
   import { autorun } from "mobx"
   import { useEffect } from "react"
   
   function Counter() {
     const { counterStore } = useRootStore()
     useEffect(() => {
       // 确保 autorun 方法只被初始化一次
       autorun(() => {
         console.log(counterStore.count)
       })
     }, []) // 这里 react 可能会报 useEffect 依赖 counterStore.count 的警告，但是我们其实就是值需要执行一遍就可以了，所以不需要添加依赖 counterStore.count
   }
   ```

2. 对于基本数据类型，属于值传递，mobx 只能跟踪到原始属性，跟踪不到复制后的值。

   ```react
   useEffect(() => {
       let count = counterStore.count
       autorun(() => {
         // 错误写法, mobx 跟踪不到变量 count
         console.log(count)
       })
     }, [])
   ```

3. 对于引用数据类型，只要引用地址不发生变化，mobx 就可以进行跟踪

   ```react
   // CounterStore.js
   class CounterStore {
     person = { name: "张三" }
   }
   ```

   ```react
   // Counter 组件
   function Counter() {
     const { counterStore } = useRootStore()
     useEffect(() => {
       const person = counterStore.person
       autorun(() => {
         console.log(person.name)
       })
     }, [])
     return (
       <div>
         <p>{counterStore.person.name}</p>
         <button onClick={() => runInAction(() => (counterStore.person.name = "李四"))}>李四</button>
         <button onClick={() => runInAction(() => (counterStore.person = { name: "王五" }))}>王五</button>
       </div>
     )
   }
   ```

4. 去除控制台中和 useEffect 相关的 ⚠️

   在 react 17 版本中，官方团队修改了脚手架工具，允许直接在外部声明 .eslintrc 文件覆盖 eslint 配置，不需要使用 package.json, react-app-rewired 和 customize-cra 就可用实现 eslint 配置

   在项目的根目录下新建 `.eslintrc.js `文件并加入如下内容

   ```react
   module.exports = {
     plugins: ["react-hooks"],
     rules: {
       "react-hooks/exhaustive-deps": 0
     }
   }
   ```

   注意此处不需要下载 [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks) 插件，`create-react-app` 脚手架工具内部已经内置。

   配置完成后重新启动应用。

###  reaction 方法

**同样是监测状态，相比于 autorun，reaction 更细，初始化不会执行，也能拿到之前的状态**

监控状态变化执行副作用，接收两个函数作为参数，第一个函数返回要监控的状态，第二个函数用来执行副作用，只有当第一个函数返回的状态发生变化时，第二个函数才会执行。reaction 方法提供了更加细颗粒度的状态控制。

和 autorun 不同，reaction 初始时不会执行副作用。

```react
import { reaction } from "mobx"

function Counter() {
	useEffect(() => {
  	reaction(
      () => counterStore.count,
      (current, previous) => { // current 是当前值，previous 是上一次的值
        console.log(current)
        console.log(previous)
      }
    )
  }, []) 
}
```



没用装饰器，装饰器还不稳定，以后不一定就是现在的用法，实际使用体验感觉也没什么。