## 概念

虚拟 DOM 本质是一个 JavaScript 对象

## 产生原因

性能需求：每次 DOM 操作都是昂贵的

开发：直接操作 DOM 不友好，也不利于维护

## 虚拟 DOM 的工作流程

**传统 App** 直接构建/修改 **DOM**，DOM 上的**事件**也是直接响应 **App** 的

**React App** 是先构建/修改 **虚拟 DOM**，虚拟 DOM 再用算法比较批处理 **实际 DOM**

**实际 DOM** 上的事件先响应到 **虚拟 DOM** ，然后再返回给 React App

大致流程如下：

1. React 状态变更（setState()）
2. 触发 Render 方法
3. 产生新的虚拟 DOM 树
4. 计算新旧 DOM 树的差异（**Diff 算法**）
5. 作用到真实 DOM

## Refs & DOM

Refs 提供了一种方式，用于访问在 render 方法中创建的 DOM 节点或 React 元素

使用场景：

- 处理焦点
- 文本选择
- 媒体控制
- 触发强制动画

Refs 的使用：通过 Refs 访问 DOM 节点

- 使用 React.createRef & ref.current

1. 创建 Ref
2. 绑定 Ref
3. 访问 DOM 节点

```jsx
// 这里只写对应位置要写的，整体不是这样的，下面那个方法的也是
constructor(props) {
    super(props)
    this.myRef = React.createRef() // 1.创建 Ref
}

render() {
    return <input ref={this.myRef}/> // 2.绑定 Ref
}

componentDidMount() {
     this.myRef.current.focus() // 3. 访问 DOM 节点
}
```

- 通过回调 Refs 获取 DOM 节点

1. 初始化一个变量存储 Ref
2. 传入回调函数直接绑定 DOM 实例
3. 访问 DOM 节点

```jsx
constructor(props) {
    super(props)
    this.myRef = null // 1.初始化一个变量存储 Ref
}

render() {
    return <input ref={ref => {this.myRef = ref}}/> // 2.传入回调函数直接绑定 DOM 实例
}

componentDidMount() {
     this.myRef.focus() // 3. 访问 DOM 节点
}
```

