# Context

## 什么是 Context？

在一般的 React 程序中，数据通过 props 自上而下由父对象传递给子对象，但是对于某些变量来说（比如全局环境变量、全局主题等）如果层层传递，会增加很多麻烦。而 Context 提供了一种在组件之间共享这些值的方法，无需通过组件层层传递 props。

在 Context 的使用中，有两种角色，一种是 Context 的生产者（Provider），通常是一个父节点，另外一种是 Context 的消费者（Consumer），通常是一个或多个子节点，因此，我们也可以将 Context 理解为一种生产者-消费者模式。

> 注：React 的 Context 的 API 随着版本更新有过较大的变动，我们这里所指的是基于 V16.3+ 的版本对应的 Context API。

## Context 的使用

这里，我们通过一个例子，来初步了解 Context 在 React 中的使用。

假设我们有一个根组件 `<APP />`，我们希望在根组件中拥有一个 `theme` 变量，这个 `theme` 变量可以控制整个系统的主题，比如按钮的颜色等等，我们这里通过 Context 来将 `theme` 传递至孙子组件。

`<App />` 的代码为：

```
const ThemeContext = React.createContext('light');

class App extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            theme: 'light'
        }
    }

    changeTheme(e){
        this.setState({
            theme: e.currentTarget.value
        })
    }

    render() {
        return (
            <ThemeContext.Provider value={this.state.theme}>
                <Toolbar />
                <div>
                    <label>dark:</label>
                    <input type="radio" value='dark' onChange={this.changeTheme.bind(this)} name="theme-radio" id="dark-radio" />
                    <label>light:</label>
                    <input type="radio" value='light' onChange={this.changeTheme.bind(this)} name="theme-radio" id="light-radio"/>
                </div>
            </ThemeContext.Provider>
        );
    }
}
```

这里我们为了验证我们的功能，使用了一个中间层级的组件 `<Toolbar />` ：

```
function Toolbar(props) {
    return (
        <div>
            <ThemedButton />
        </div>
    );
}
```

我们最终的目标组件为：

```
function Button(props) {
    return (
        <button className={props.theme + "-button"}>ContextButton</button>
    )

}
```

这个时候，我们可以分别编写 `.dark-button` 和 `.light-button` 来设置不同主题下按钮的样式。

我们可以发现，中间层级的 `<Toolbar />` 组件并没有显式地传递相关 props，在这里使用 Context 给我们的项目带来了方便，并且更为简洁。

更多的关于 Context 的例子，可以参考[ React 官方参考资料](https://reactjs.org/docs/context.html#dynamic-context)

## Context 的进一步理解

在 React 官方文档中，Context 被归类为高级部分，属于 React 的高级 API，官方并不建议在稳定版本的项目中过多使用 Context。

实际上，在项目中使用 Context API，虽然会减少一些显式的 props 传递，但如果 Context 传递的内容过多反而会导致数据流向的不清晰，从而让我们的应用变得难以维护和调试，我们在实际使用中，需要注意其中的平衡。