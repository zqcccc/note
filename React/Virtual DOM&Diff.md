# Virtual DOM & Diff

在实现一个 tiny react 的时候，首先想到的是 jsx 被 babel 转换的时候，如果我们这里 React 不叫 React 的话，babel 将 jsx 转换成 React.createElement 要怎么改就是个问题

可以在文件的开头加上

```js
/** @jsx TinyReact.createElement */
// 这样 React.createElement 就变成了 TinyReact.createElement
```

上面的方法需要去每个文件里去加这样的注释，显然不适合多文件的写法，所以我们需要配置 babel

```json
// .babelrc
{
  "presets": [
    "@babel/preset-env",
    [
      "@babel/preset-react",
      {
        "pragma": "TinyReact.createElement"
      }
    ]
  ]
}
```

createElement 函数就是根据传入的参数生成 virtual dom

```js
export default function createElement(type, props, ...children) {
  const childElements = [].concat(...children).reduce((result, child) => {
    if (child !== false && child !== true && child !== null) {
      if (child instanceof Object) {
        result.push(child)
      } else {
        result.push(createElement("text", { textContent: child }))
      }
    }
    return result
  }, [])
  return {
    type,
    props: Object.assign({ children: childElements }, props),
    children: childElements
  }
}
```

非布尔值且非 null 且非对象的 children 要处理成 text 类型的 virtual dom

还要加上 children 和 props 到 virtual dom 返回

TinyReact 首先要完成一个 render 方法用来将 virtual dom 转换成真实 dom

render 函数里有多种情况要考虑：

- 直接转换成真实 dom
- 当页面中有旧的 dom，要做对比

上面两种情况都是进入 diff 函数去判断

diff 中不存在旧的 DOM 的时候就直接调用 mountElement 方法将 virtual dom 转换成真实 dom

在 mountElement 里要判断是组件还是普通的 virtual dom，在创建 children 元素的时候递归创建就行了

