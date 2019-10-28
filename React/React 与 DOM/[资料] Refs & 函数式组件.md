# Refs & 函数式组件

## Refs

> 注：本扩展所讲解的 Refs 基于 React 16 以及以后的版本.

Refs 提供了一种访问 React 组件渲染之后对应的 Dom 节点的方式。

一般情况下，我们在以下几种场景中可能会用到 refs：

- 管理焦点、文本选择或媒体播放
- 触发动画
- 与第三方 DOM 库集成或者交互

一般来说，我们要尽可能地减少直接使用 Refs 的情况，避免将 Refs 用于可以声明性地完成地的任何操作。

### 创建 Refs

Refs 是使用 `React.createRef()` 创建的，并通过 ref 属性附加到 React 元素。 在构造组件时，通常将 Refs 分配给实例属性，以便可以在整个组件中引用它们。

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

### 访问 Refs

如果一个 ref 已经被传递到了 render 函数中的一个元素，那么一个该节点的 Dom 引用可以通过访问 `current` 属性来获得：

```
const node = this.myRef.current;
```

ref 的值根据节点的类型而有所不同：

- 当 ref 属性用于 HTML 元素时，在构造函数中使用 `React.createRef()` 创建的 ref 将接收底层 DOM 元素作为其当前属性。
- 当 ref 属性用于自定义组件的时候，ref 对象将把这个自定义组件的已经挂载的实例作为其 current 属性值。
- 你不应该把 ref 属性用于函数式组件因为他们并没有实例。

## 函数式组件

React 函数式组件是相对于 React 基于 Class 类声明的组件的另外一种写法，其具有以下特性：

- 不需要声明类，可以避免大量的譬如 extends 或者 constructor 这样的代码。
- 不需要显式声明 this 关键字，在 ES6 的类声明中往往需要将函数的 this 关键字绑定到当前作用域，而因为函数式声明的特性，我们不需要再强制绑定。
- 易于理解与测试。
- 更佳的性能表现：因为函数式组件中并不需要进行生命周期的管理与状态管理，因此 React 并不需要进行某些特定的检查或者内存分配，从而保证了更好的性能表现。

下面是一个基于 Class 类声明的组件：

```
class Text extends React.Component {
  constructor(props) {
      super(props);
  }  
  render() {
    return <p>{this.props.children}</p>;
  }
}
```

如果变换成函数式组件写法，则：

```
const Text = (props) =>
  <p>{props.children}</p>;
```

另外，函数式组件也可以访问 context：

```
class App extends React.Component {
  static childContextTypes = {
    fontFamily: React.PropTypes.string
  }
  getChildContext() {
    return {
      fontFamily: 'Helvetica Neue'
    };
  }
  render() {
    return <Text>Hello World</Text>;
  }
}

const Text = (props, context) =>
  <p style={context}>props.children</p>;

Text.contextTypes = {
  fontFamily: React.PropTypes.string
};

//代码参考：https://segmentfault.com/a/1190000006180667
```

