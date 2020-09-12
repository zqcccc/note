# Provider 与 connect 解析

Provider 与 connect 都来源于 react-redux 库，通过它们可以十分便捷的实现多个组件间的 Redux 数据共享。简单来说，Provider 作为组件最外层的包裹，充当一个数据源的角色，而 connect 则为所包裹的子组件提供访问该数据的能力。

## Provider

Provider 本质上是一个 React 组件，在应用中，Provider 只需要完成两件事：

- 对应用最外层的组件做了一层包裹，使程序中所有的组件都成为 Provider 的子组件。
- 接收 Redux 的 Store 作为属性，并将其传递给子组件。

### 例子

```
import ReactDom from 'react-dom';
import App from './App.jsx';
import { Provider } from 'react-redux';
import store from '../Store/store';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('app')
)
```

以上，Provider 将应用的最外层的组件做了一层包裹，保证了应用中所有的组件都从属在它的下面。如果没有 Provider 的这一层包裹，在子组件中使用的 connect 方法将无法生效。

### 原理解析

Provider 使用了 React 中的 context 对象，向多个层级下的子组件传递 store。Provider 的子组件不能直接获取到这个 context 对象的值，但通过使用 connect 方法，可以很方便的获取到该 store 值。

以下是 react-redux 中 Provider 模块的部分源码(版本为 5.0.7)：

```js
export function createProvider(storeKey = 'store', subKey) {
    const subscriptionKey = subKey || `${storeKey}Subscription`

    class Provider extends Component {
        // 设置 context 数据源
        getChildContext() {
          return { [storeKey]: this[storeKey], [subscriptionKey]: null }
        }

        constructor(props, context) {
          super(props, context)
          // 获取传入的 store 属性
          this[storeKey] = props.store;
        }

        render() {
          return Children.only(this.props.children)
        }
    }

    if (process.env.NODE_ENV !== 'production') {
      Provider.prototype.componentWillReceiveProps = function (nextProps) {
        if (this[storeKey] !== nextProps.store) {
          warnAboutReceivingStore()
        }
      }
    }

    Provider.propTypes = {
        store: storeShape.isRequired,
        children: PropTypes.element.isRequired,
    }
    // 通过 childContextTypes 指定 getChildContext 方法中传递给子组件的数据类型，不指定则会报错
    Provider.childContextTypes = {
        [storeKey]: storeShape.isRequired,
        [subscriptionKey]: subscriptionShape,
    }

    return Provider
}

export default createProvider()
```

需要注意，React 在 16.3 开始使用新的 context Api，我们文档中也有关于新版 context Api 的介绍。而目前最新版的 react-redux （5.0.7）仍旧使用 React 16.3 之前版本的 context Api。我们在此不再介绍旧版 context Api 的用法，感兴趣的用户可以查阅官方文档自行了解（[官方文档英文版](https://reactjs.org/docs/legacy-context.html)、[官方文档中文版](https://react.docschina.org/docs/legacy-context.html)）

## connect

connect 作用就是为 Provider 子组件赋予访问 Store 的能力，它接收了 Provider 设置的 context 对象，并将其所提供 store 中的 state 和 actions 通过属性的方式绑定到子组件上。connect 方法的执行可以分为以下两步：

- 接收四个参数，返回一个高阶组件。
- 高阶组件接收子组件，返回一个新的组件，新组件可以通过 this.props 访问到 Provider 所传递 store 中的 state 和 actions.

以下是官方对 connect 方法的定义：

```JavaScript
connect([mapStateToProps],  [mapDispatchToProps],  [mergeProps],  [options])
```

我们分别来介绍这几个参数的作用和用法。

### mapStateToProps

该参数是一个函数，它帮助我们把 Store 中的 State 转换成 Props 并传递到新的组件中。

我们一般可以这么编写这个函数：

```
const mapStateToProps = state => ({state})
```

也可以只获取需要的属性：

```
const mapStateToProps = (state) => {
    return {
        list: state.list,
    }
}
```

mapStateToProps 函数还有第二个参数 ownProps，即传入组件自身的 Props，可以灵活判断是否需要使用到该参数。
需要注意的是，当 mapStateToProps 的值为假值（结果为 false 的值）的时候，则代表不监听 Store，子组件也无法获取到对应的 state 值。

### mapDispatchToProps

对应 mapStateToProps，mapDispatchToProps 帮助我们把 action 通过属性的方式传递给子组件。这个参数的类型可以是函数/对象或者假值。

先引入 Actions.js

```
import * as actionCreators from '../Actions';
```

我们直接将该参数设置成函数：

```
const mapDispatcherToProps = (dispatch, ownProps) => {
    return {
        addItem () {
            dispatch(actionCreators.addItem())
        }
    }
}
```

也可以设置成对象：

```
const mapDispatcherToProps = {
  addItem: actionCreators.addItem
}
```

也可以使用 Redux 提供的 bindActionCreators 方法：

```
import {bindActionCreators} from 'redux';

const mapDispatchToProps = (dispatch, ownProps) => {
  return bindActionCreators(actionCreators, dispatch);
}
```

当 mapDispatcherToProps 为假值时，react-redux 则会为我们注入 dispatch，Store 的 dispatch 方法将会被直接返回，并将充当子组件的一个属性。此时我们可以直接使用该 dispatch 属性分发 Action 执行数据的更新。

### mergeProps

该参数可以把组件自身属性 ownProps 与前两个参数所设置的 stateProps 和 dispatchProps 属性合并起来。通过设置该参数，我们可以在这三个属性中只提取我们需要的属性。

例如，我们只提取 stateProps 和 dispatchProps 的部分属性：

```
const mergeProps = (stateProps, dispatchProps, ownProps) => {
  return Object.assign({}, ownProps, {
    list: stateProps.list,
    addItem: () => dispatchProps.add(),
  })
}
```

如果不设置该参数，则会默认使用 Object.assign({}, ownProps, stateProps, dispatchProps) 进行属性合并。

### options

该参数用于定制 connect 方法的行为，我们几乎不需要用到这个参数。感兴趣的同学可以自行查阅了解或点击 [react-redux 文档](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#api)。