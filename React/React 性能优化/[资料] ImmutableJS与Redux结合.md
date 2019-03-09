# ImmutableJS 与 Redux 结合

Immutable.JS 和 Redux 的结合，可以为应用提供可靠显著的性能优化，但前提在于如何正确的使用。

## Immutable.JS 的正确使用方式

### 使用 ImmutableJS 封装 Redux state tree

不要将 JavaScript 对象和 ImmutableJS 混合使用，使用了ImmutableJS，我们可以完全摒弃普通的 JavaScript 对象。

- 使用 Immutable.JS 的 fromJS() 函数创建 Redux state tree。
- 使用 redux-immutable 中的 combineReducers 方法替代 Redux 自身的 combineReducers 方法，因为后者会将 state tree 变成一个普通的 JavaScript 对象。
- 把一个普通的 JavaScript 对象添加到 Immutable.JS 的 Map 或者 List 中时，先用 fromJS() 方法将其转换为 Immutable.JS 对象。

### 限制对 toJS() 的使用

toJS() 函数十分耗费性能，应当尽量避免使用该函数。

### 不要在 mapStateToProps 函数中使用 toJs()

toJS() 函数每次都会返回一个新的对象，在 mapStateToProps 中执行此操作，将会导致不必要的重新渲染。

### 不要在 Dumb 组件中使用 Immutable.JS

Dumb 组件应该作为一个纯组件，相同的输入会得到相同的输出，且不会有任何的外部依赖。如果把 Immutable.JS 类型的数据作为属性传入 Dumb 组件，则意味着它需要使用 Immutable.JS 对应的方法去获取这个属性的值，这种依赖性导致 Dumb 组件不纯，且难以复用。

### 尽可能使用 Immutable.JS

除 Dumb 组件以外，我们应该尽可能在应用中使用 Immutable.JS，它可以在任何地方为我们保证代码的高性能。

## 使用 Immutable.JS 会有什么问题?

### 一旦使用，Immutable.JS 将遍布整个应用

使用 Immutable.JS 封装 Redux 状态树，意味着应用中所有组件都需要通过 Immutable.JS 提供的 Api 去获取 Redux 数据，这个影响将遍布整个应用。同时的，这也使得以后移除 Immutable.JS 变得十分困难。

### 代码变得更加冗余

使用 Immutable.JS，意味着我们放弃了通过标准 JavaScript 中的点语法或中括号引用对象的属性。同时的，我们也无法对 Immutable.JS 对象使用解构或展开运算符。

### 不适用于经常改变的小数值

当我们的数据包含大量小而简单的 JavaScript 对象时，速度会变得很慢。Immutable.JS 更适合用于比较大型的数据集合，例如 Redux state tree。

### 难以调试

Immutable.JS 对象封装了其特定的属性，而这些属性并不是我们所关注的。而我们真正想要看到的数据实际上都被封装了几层，检查数据的时候十分不方便。不过我们可以通过安装谷歌插件 [Immutable.js 对象格式化扩展](https://chrome.google.com/webstore/detail/immutablejs-object-format/hgldghadipiblonfkkicmgcbbijnpeog) 解决此问题。

## 利大于弊

使用 Immutable.JS 可以保证组件只在需要它渲染时渲染，这个就避免了许多可能在渲染中出现的问题，例如组件在不需要渲染时重新渲染，需要渲染时又不执行渲染。同时，Immutable.JS 帮助我们实现了性能更佳的 React 应用，相比它所带来的弊端，Immutable.JS 还是很值得使用的。