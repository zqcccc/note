# Redux DevTools

在软件应用的开发中，调试是必不可少的一个环节，选择一款合适的调试工具，有助于提高我们的开发效率。
对于搭配 Redux 框架开发的 React 应用而言，Redux DevTools 正是这样一款调试工具。使用 Redux DevTools，我们可以很清晰的看到 Redux 应用当前 Store 的状态，以及 Action 触发时的数据变化。它的功能不止于此，还有更多的功能，安装后大家可自行摸索。

## 安装与配置

Redux DevTools 的安装可分为两个部分，一个是浏览器插件的安装，另一个则是代码中的插件配置，二者缺一不可。

### 1. 安装 Redux DevTools 浏览器插件

打开谷歌的网上应用店（[链接](https://chrome.google.com/webstore/search/redux%20devtools?hl=zh-CN)）或者火狐的附件组件商店（[链接](https://addons.mozilla.org/zh-CN/firefox/?utm_source=discovery.addons.mozilla.org&utm_medium=firefox-browser&utm_content=find-more-link-top&src=api)），搜索 redux devtools，直接安装即可。

### 2. 代码配置

插件安装完后，我们需要在创建 Redux Store 的时候进行插件的配置。

#### 基础配置

当我们的 Store 不包含中间件时，我们可以直接配置：

```
const store = createStore(
   reducer, 
   /* preloadedState, */
+  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
 );
```

`window.__REDUX_DEVTOOLS_EXTENSION__` 方法是 Redux DevTools 浏览器插件为我们定义好的方法变量。当浏览器未安装 Redux DevTools 插件时，该方法变量值为 undefined。

#### 包含中间件的配置

当 Store 包含中间件，配置如下：

```
import { createStore, applyMiddleware, compose } from 'redux';

+ const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
+ const store = createStore(reducer, /* preloadedState, */ composeEnhancers(
- const store = createStore(reducer, /* preloadedState, */ compose(
    applyMiddleware(...middleware)
  ));
```

`window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__` 函数同样来源于 Redux DevTools 插件，当插件未安装时，直接使用该方法会导致报错，所有这里加多了一层保护，当 `window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__` 值为 false 时，使用 Redux 提供的 `compose` 方法作为替代。

#### 使用 redux-devtools-extension 包进行配置

redux-devtools-extension 包含了 redux-devtools 插件配置的所有方法，使用 redux-devtools-extension，我们的配置不再依赖于插件提供的方法变量，可以减少繁杂的容错代码。

安装 npm 包：

```
npm install --save-dev redux-devtools-extension
```

基础配置：

```
import { createStore } from 'redux';
import { devToolsEnhancer } from 'redux-devtools-extension';

const store = createStore(
    reducer, 
    /* preloadedState, */
    devToolsEnhancer()
);
```

包含中间件的配置：

```
import { createStore, applyMiddleware } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(
    reducer, 
    /* preloadedState, */
    composeWithDevTools(
      applyMiddleware(...middleware)
    )
);
```

#### 更多配置

以上的配置已经足够我们日常的开发调试，但除此之外，Redux DevTools 还可以做一些定制化的配置。例如，我们可以配置 action 分发的延时（即把一个间隔时间内分发的多个 aciton 收集起来一起分发），也可以配置只在开发环境开启 Redux DevTools 插件。更多相关的配置可以参考 [官方文档](https://github.com/zalmoxisus/redux-devtools-extension)。