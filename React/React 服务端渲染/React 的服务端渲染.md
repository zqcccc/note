## react 渲染函数

```jsx
ReactDOM.render(
	element,
    document.getElementById('root')
) // 传入需要被渲染的 element 实例（即 React 组件）和 DOM 容器，完成渲染
```

## 服务端 react 渲染函数

```jsx
ReactDOMServer.renderToString(element)
```

输入一个根组件，React 将会返回一段 HTML 字符串

如果在一个已经有了服务端渲染标记的节点上调用 `ReactDOM.render()` ，React 将保留该节点

ReactDOMServer.renderToString(element) 可以在调用 ReactDOM.render() 时保留该节点，而 ReactDOMServer.renderToStaticMarkup(element) 由于缺少必要的属性，不能实现相关效果

仅作绑定事件处理，这会让你有一个非常高效的初次加载体验

```jsx
ReactDOMServer.renderToStaticMarkup(element)
```

类似 renderToString，但是不会创建额外的 DOM 属性，例如 data-reactid 这些仅在 React 內部使用的属性。如果你希望把 React 当作一个简单的静态页面生成器来使用，这很有用，因为去掉额外的属性可以节省很多字节

服务端渲染基于 Node.js 环境，并没有 window 对象
服务端渲染由于带有更丰富的数据和标签，因此能够有效提高 SEO 的效果
服务端渲染返回的仍然是 HTML 字节流，由浏览器最终渲染成 DOM 节点
React 服务端渲染可以借助 React-Router 提供的 matchPath 等API，做到路由级别的服务端渲染

### 服务端渲染流程

1. 收到浏览器请求
2. 拉取需要的数据
3. 模板渲染
4. 返回给用户

细节：

1. 组件初始化
2. construtor
3. getDerivedStateFromProps
4. render
5. componentDidMount

步骤 1~4 都是在服务端完成的，服务端渲染完成后，剩下的生命周期都会在客户端完成

### 服务端收到请求并拉取数据

```js
let response = await fetch('https://www.apiopen.top/journalismApi')
const typeLists = await response.json()
```

### 利用请求的数据渲染并返回