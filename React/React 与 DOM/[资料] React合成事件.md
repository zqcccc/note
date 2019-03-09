# React 合成事件

React 中，如果需要绑定事件，我们常常在 JSX 中这么写：

```jsx
<div onClick={this.onClick}>
    Hello World
</div>v
```

但这个时候，这个 `onClick` 实际上是 React 的合成事件。React 并不是将 click 事件绑在该 div 的真实 DOM 上，而是在 document 处监听所有支持的事件，当事件发生并冒泡至 document 处时，React 将事件内容封装并交由真正的处理函数运行。

相关流程如下图所示：

![img](http://p.qpic.cn/qqconadmin/0/8bcd082fd1224cdcbda150c00a2a243a/0)

其中，由于 event 对象是复用的，事件处理函数执行完后，属性会被清空，所以 event 的属性无法被异步访问。

## React 原生事件与合成事件

如果我们想在 React 中使用原生的 dom 事件，我们可以通过 ref 的方式拿到对应组件生成的 dom 元素，然后在对应的 dom 元素上绑定事件，不过一般，并不推荐这种做法。

以下是一个共同使用原生事件与合成事件的 Demo：

```jsx
class App extends Component {

    constructor(props){
        super(props);
        this.ref1 = React.createRef();
    }

    componentDidMount(){
        this.ref1.current.addEventListener('click', (e) => {
            console.log('Dom Handle Click', e);
        })
    }

    handle(e){
        console.log('React Handle Click', e);
    }

    render() {
        return (
            <div className="App" onClick={this.handle.bind(this)} ref={this.ref1} >
                Hello world
            </div>
        );
    }
}

export default App;
```

我们如果运行之后便会发现，其打印结果为：

```
Dom Handle Click
React Handle Click
```

这也验证了 React 其实是在事件冒泡到 document 时才开始做处理的。

这时候，我们如果阻止事件冒泡，那么事件是不会到达 React 的合成事件回调函数中的。

我们把 `componentDidMount` 中的内容稍作修改：

```jsx
componentDidMount(){
     this.ref1.current.addEventListener('click', (e) => {
         console.log('Dom Handle Click', e);
         e.stopPropagation()
     }, false)
}
```

这个时候则只会打印：

```
Dom Handle Click
```

以上就是 React 合成事件的相关介绍。