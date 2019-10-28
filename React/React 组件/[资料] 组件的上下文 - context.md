# 组件的上下文

> Context 通过组件树提供了一个传递数据的方法，从而避免了在每一个层级手动的传递 props 属性。

在一个典型的 React 应用中，数据是通过 props 属性由上向下（由父及子）进行传递的，但在某些情况下这种传递是极其繁琐的。例如地区偏好和 UI 主题。Context 提供了一种在组件之间共享此类值的方式，而不必通过组件树的每个层级显式地传递 props 。

## 例子

我们来实现一个例子，这个例子中通过一个 'theme' 属性调整一个按钮的样式。

这是没使用 Context 的例子：

```
function ThemedButton(props) {
  return <Button theme={props.theme} />;
}

// 中间组件
function Toolbar(props) {
  // Toolbar 组件必须添加一个额外的 theme 属性
  // 然后传递它给 ThemedButton 组件
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  );
}

class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />;
  }
}
```

不使用 Context 的数据流为：

App 组件 > 中间组件 Toolbar > 按钮组件 ThemedButton



使用Context：

```
// 创建一个 theme Context,  默认 theme 的值为 light
const ThemeContext = React.createContext('light');

function ThemedButton(props) {
  // ThemedButton 组件从 context 接收 theme
  return (
    <ThemeContext.Consumer>
      {theme => <Button {...props} theme={theme} />}
    </ThemeContext.Consumer>
  );
}

// 中间组件
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class App extends React.Component {
  render() {
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}
```

## React.createContext

该方法创建一对 { Provider, Consumer }。
在上述 Context 例子中，App 组件使用了 Context 的 Provider Api，而 ThemedButton 则使用了 Consumer。
当 React 渲染 ThemedButton 组件时，它将从组件树的上层中最接近的匹配的 Provider （App 组件），并读取当前的 context 值（对应例子中的'dark'值）。

## Provider

```
<Provider value={/* 该值将被子层 Consumer 接收 */}>
```

Provider 接收了一个 value 属性，并将其传递给 Provider 的后代 Consumer。

一个 Provider 可以联结到多个 Consumer，且可以在组件树中被嵌套使用。

## Consumer

```
<Consumer>
  {value => /* 返回一个React 节点 */}
</Consumer>
```

Consumer 是一个可以订阅 context 变化的 React 组件。它的子节点是一个函数，该函数接收当前 context 的值作为第一个参数，并返回一个 React 节点。传递给函数的 value 值等于组件树中上层 context 最近的 Provider 的 value 属性值。如果 context 没有 Provider ，那么 value 参数将等于被传递给 createContext() 的 defaultValue。每当 Provider 的值发生改变时，所有的 Consumer 都将会重新渲染。

