# React SSR

## 什么是客户端渲染

CSR: Client Side Rendering

服务端近返回 JSON 数据，DATA 和 HTML 在客户端进行渲染

## 什么是服务端渲染

SSR: Server Side Rendering

服务端近返回 HTML，DATA 和 HTML 在服务端进行渲染

直接用 react 构建的都是客户端渲染

## 客户端渲染存在的问题

1. 首屏等待时间长，用户体验差
2. 页面结构为空，不利于 SEO

## React SSR 同构

同构指的是代码复用，即实现客户端和服务端最大程度的代码复用，这也是 React SSR 的核心

### 项目结构

```
react-ssr
	-src 源代码
		-client 客户端代码
		-server 服务端代码
		-share 同构代码
```

### 创建 Node 服务器

```js
/* server/http.js */
import express from 'express';

const app = express();

app.use(express.static('public'));

app.listen(3000, () => console.log('app is running on 3000 port'));

export default app;
```

### 实现 React SSR

1. 引入要渲染的 React 组件
2. 通过 `renderToString` 方法将 React 组件转换为 HTML 字符串
3. 将结果 HTML 字符串响应给客户端

`renderToString` 方法用于将 React 组件转换为 HTML，通过 `react-dom/server` 导入

share 目录下放服务端客户端都可以用的 pages 页面的代码

```jsx
/* server/index.js */
import app from './http'
import Home from '../share/pages/HomePage'
import { renderToString } from 'react-dom/server'

app.get('/', (req, res) => {
  const content = renderToString(<Home />)
  res.send(`
		<html>
      <head>
        <title>React SSR</title>
      </head>
      <body>
        <div id="root">${content}</div>
      </body>
		</html>
	`)
});
```

### webpack 打包配置

当前现在的代码有 es module 模块和 jsx 语法所以在 node 里是运行不起来的，我们需要 webpack 打包后再启动服务

```js
/* webpack.servre.js */
const path = require('path')

module.exports = {
  mode: 'development',
  target: 'node',
  entry: './src/server/index.js',
  output: {
    path: path.join(__dirname, 'build'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react'] // 转换高版本js代码和react语法
          }
        }
      }
    ]
  }
}
```

在 `package.json` 里配置一个 `script`

```json
  "scripts": {
    "dev:server-build": "webpack --config webpack.server.js"
  },
```

先执行 `yarn dev:server-build` 打包然后在执行 `node build/bundle.js` 然后在浏览器里访问 `localhost:3000` 就可以看到 react 的 home 页面了（目前只是简单的静态页面）

以上这样都比较麻烦，需要我们去配置一下script

```json
  "scripts": {
    "dev:server-run": "nodemon --watch build --exec \"node build/bundle.js\"",
    "dev:server-build": "webpack --config webpack.server.js --watch",
  },
```

### react 附加事件

现在的 react 组件写了一个 onClick 事件

```jsx
import React from "react"

function Home() {
  return <div onClick={() => console.log("Hello")}>
    Home works1
  </div>;
}

export default Home
```

在页面点击并不会触发事件，查看页面源代码可以发现，页面并没有加载任何 js，这自然也不会有任何事件绑定了

解决这个问题的思路是通过客户端二次“渲染” hydrate

使用 hydrate 方法对组件进行渲染，为组件元素附加事件

hydrate 方法在实现渲染的时候，会复用原本已经存在的 DOM 节点，减少重新生成节点以及删除原本 DOM 节点的开销。

通过 react-dom 导入 hydrate

```jsx
ReactDOM.hydrate(<App />, document.querySelector('#root'))
```

于是会有下面的代码

```jsx
/* client/index.js */
import React from 'react'
import ReactDOM from 'react-dom'

import Home from '../share/pages/Home'

ReactDOM.hydrate(<Home />, document.getElementById('root'))
```

这个是纯客户端代码，在 node 环境是运行不起来的，我们要去对客户端代码进行 webpack 打包

### 客户端 React 打包配置

1. webpack 配置
   - 打包目的：转换 JSX 语法，转换浏览器不识别的高级 JavaScript 语法
   - 打包目标位置： public 文件夹
2. 打包启动命令配置
   - `"dev:client-build": "webpack --config webpack.client.js --watch"`

webpack 配置和 `webpack.server.js` 没有差很多

```js
const path = require('path')

module.exports = {
  mode: 'development',
  entry: './src/client/index.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react']
          }
        }
      }
    ]
  }
}
```

### 添加客户端包文件请求链接

```html
		<html>
      <head>
        <title>React SSR</title>
      </head>
      <body>
        <div id="root">${content}</div>
        <script src="bundle.js"></script>
      </body>
		</html>
```

请求的 `bundle.js` 已经是打包到 public 了，后端的 `express` 已经是为 `public` 提供了静态服务了，客户端 javascript 打包文件会作为静态资源使用

### 优化

1. 合并 webpack 配置

服务端 webpack 配置和客户端 webpack 配置存在重复，将重复配置抽象到 `webpack.base.js` 配置文件中

```js
const path = require("path");
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base');

const config = { /* 这里就是一些 client 或者 server 特殊的配置 */ };

module.exports = merge(baseConfig, config);

```

2. 合并项目启动命令

目的：使用一个命令启动项目，解决多个命令启动的繁琐问题，通过 `npm-run-all` 工具实现

```json
"dev": "npm-run-all --parallel dev:*"
```

3. 服务起端打包文件体积优化

问题：在服务端打包文件中，包含了 Node 系统模块，导致打包文件本身体积庞大

解决：通过 webpack 配置剔除打包文件中的 Node 模块

```js
/* webpack.server.js */
const nodeExternals = require('webpack-node-externals');

const config = {
  externals: [nodeExternals()]
}
```

4. 将启动服务器代码和渲染代码进行模块化拆分

优化代码组织方式，渲染 React 组件代码是独立功能，所以把它从服务端入口文件中进行抽离

把服务端代码中的 React 部分抽离到 `renderer.js` 中

### 路由支持

实现思路分析：

在 React SSR 项目中需要实现两端路由

客户端路由是用于支持用户通过通过点击链接的形式跳转页面

服务端路由是用于支持直接从浏览器地址栏中访问页面

客户端和服务端公用一套路由规则

编写路由规则

```js
/* share/routes.js */
import Home from '../share/pages/Home';
import List from '../share/pages/List';

export default [{
  path: '/',
  component: Home,
  exact: true
}, {
  path: '/list',
  ...List
}]
```

#### 实现服务器端路由

1. Express 路由接受任何请求

Express 路由接受所有 GET 请求，服务器端 React 路由通过请求路径匹配要进行渲染的组件

```js
app.get('*', async (req, res) => {})
```

2. 服务器端路由配置

```jsx
/* server/renderer.js */
import React from 'react'
import { renderToString } from 'react-dom/server'
import { StaticRouter } from 'react-router-dom'
import { renderRoutes } from 'react-router-config' // 路由文件是数组形式的，需要用这个变一下
import routes from '../share/routes'

export default req => {
  const content = renderToString(
    <StaticRouter location={req.path}>
      {renderRoutes(routes)}
    </StaticRouter>
  )
  return `
  <html>
    <head>
      <title>React SSR</title>
    </head>
    <body>
      <div id="root">${content}</div>
      <script src="bundle.js"></script>
    </body>
  </html>
`
}
```

路由文件

```js
/* share/routes.js */
import Home from '../share/pages/Home';
import List from '../share/pages/List';

export default [{
  path: '/',
  component: Home,
  exact: true
}, {
  path: '/list',
  ...List
}]
```

#### 实现客户端路由

添加客户端路由配置

```jsx
/* client/index.js */
import { BrowserRouter } from 'react-router-dom'
import { renderRoutes } from 'react-router-config'
import routes from '../share/routes'

ReactDOM.hydrate(
	<BrowserRouter>
  	{renderRoutes(routes)}
  </BrowserRouter>,
  document.querySelector('#root')
)
```

在 Home 组件中添加一个 Link 组件试试路由跳转

```jsx
/* share/pages/Home.js */
import { Link } from 'react-router-dom';

function Home() {
  return <div onClick={() => console.log("Hello")}>
    Home works 123
    <Link to="/list">jump to list</Link>
  </div>;
}
```

### Redux 支持

实现思路

在实现了 React SSR 的项目中需要实现两端 Redux

客户端 Redux 就是通过客户端 JavaScript 管理 Store 中的数据

服务端 Redux 就是在服务器端搭建一套 Redux 代码，用于管理组件中的数据

客户端和服务端公用一套 Reducer 代码

创建 Store 的代码由于参数传递不同所以不可以公用

#### 实现客户端 Redux

1. 创建 Store

```js
/* client/createStore.js */
import { applyMiddleware, createStore } from 'redux';
import thunk from 'redux-thunk';
import reducer from '../share/store/reducers';

const store = createStore(reducer, {}, applyMiddleware(thunk));

export default store;
```

使用

```jsx
/* client/index.js */
import { Provider } from 'react-redux'
import store from './createStore'

ReactDOM.hydrate(
  <Provider store={store}>
    <BrowserRouter>
      {renderRoutes(routes)}
    </BrowserRouter>
  </Provider>
	,
  document.querySelector('#root')
)
```

2. 配置

Reducer

```js
/* share/reducers/index.js */
import { combineReducers } from 'redux';
import userReducer from './user.reducer';

// {user: []}
export default combineReducers({
  user: userReducer
});

/* share/reducers/user.reducer.js */
import { SAVE_USER } from "../actions/user.action";

export default (state = [], action) => {
  switch(action.type) {
    case SAVE_USER:
      return action.payload.data;
    default:
      return state;
  }
}
```

Action

```js
/* share/actions/user.action.js */
import axios from 'axios';

export const SAVE_USER = 'save_user';

// 发送请求 获取用户列表数据
export const fetchUser = () => async dispatch => {
  // let response = await axios.get('https://jsonplaceholder.typicode.com/users');
  let response = {
    data: [{id: 1, name: "</script><script>alert(1)</script>"}]
  }
  dispatch({
    type: SAVE_USER,
    payload: response
  })
}
```

#### 实现服务端 Redux

1. 创建 Store

```js
/* server/createStore.js */
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import reducer from '../share/store/reducers';

export default () => createStore(reducer, {}, applyMiddleware(thunk))
```

2. 配置 Store

```js
app.get('*', async (req, res) => {
  const store = createStore()
  res.send(renderer(req, store))
})
```

3. 服务端 store 数据填充

问题：服务端创建的 store 是空的，组件并不能从 Store 中获取到任何数据

解决：服务器端在渲染组件之前获取到组件所需要的数据

- 在组件中添加 `loadData` 方法，此方法用于获取组件所需数据，方法被服务端调用
- 将 `loadData` 方法保存在当前组件的路由信息对象中
- 服务端在接受到请求后， 根据请求地址匹配出要渲染的组件的路由信息
- 从路由信息中获取组件中的 `loadData` 方法并调用方法获取组件所需数据
- 当数据获取完成以后再渲染组件并将结果响应到客户端

```js
/* share/pages/List.js */
function loadData (store) { // 这个 store 其实是服务端生成的 store
  return store.dispatch(fetchUser())
}
```

4. React 警告消除

现在访问 `/list` 页面，react 应该有警告服务端返回的 html 的 ul 里不应该有 li 元素

原因：客户端 Store 在厨师状态下是没有数据的，在渲染组件的时候生成了空的 ul，但是服务端是先获取数据在进行的组件渲染，所以生成的是有子元素的 ul，hydrate 方法在对比的时候发现两者不一致，所以报了警告

解决思路：将服务端获取到的数据回填给客户端，让客户端拥有初始数据

```jsx
/* server/renderer.js */
const initialState = JSON.stringify(store.getState())
    <body>
      <div id="root">${content}</div>
      <script>window.INITIAL_STATE = ${initialState}</script>
      <script src="bundle.js"></script>
    </body>
```

客户端 Store 接收

```js
/* client/createStore.js */
const store = createSstore(reducer, window.INITIAL_STATE, applyMiddleware(thunk));
```

### 防范 XSS 攻击

转义状态中的恶意代码

```js
let response = {
    data: [{id: 1, name: "</script><script>alert(1)</script>"}]
  }
import serialize from 'serialize-javascript'
const initialState = serialize(store.getState())
```

# Next.js

Next.js 是 React 服务端渲染应用框架，用于构建 SEO 友好的 SPA 应用

1. 支持两种预渲染方式，静态生成和服务器端渲染
2. 基于页面的路由系统，路由零配置
3. 自动代码拆分，优化页面加载速度
4. 支持静态导出，可将应用导出为静态网站
5. 内置 `css-in-js` 库 `styled-jsx`
6. 方案成熟，可用于生产环境，世界许多公司都在使用
7. 应用部署简单，拥有专属部署环境 `Vercel`，也可以部署在其它环境

## 创建 next.js 项目

创建：`npm init next-app next-guide`

运行：`npm run dev`

访问：`localhost:3000`

临时安装 `create-next-app` 用于创建 Next.js 项目

## 创建页面

在 Next.js 中，页面是放在 pages 文件夹中的 React 组件

组件需要被默认导出

组件文件中不需要引入 React

页面地址与文件地址是对应的关系

## 基于页面的路由系统

页面跳转

`Link` 组件默认使用 JavaScript 进行页面跳转，即 SPA 形式的跳转

如果浏览器中 JavaScript 被禁用，则使用链接跳转

`Link` 组件中不应添加除 href 属性以外的属性，其余属性添加到子 a 标签上

`Link` 组件通过预取（在生产中）功能自动优化应用程序以获得最佳性能

```js
import Link from 'next/link'
```



## 静态资源、元数据和 CSS

### 静态资源

应用程序根目录中的 public 文件夹用于提供静态资源

通过以下形式进行访问

`public/images/1.jpg => /images/1.jpg`

`public/css/base.css => /css/base.css`

### 修改页面元数据

通过 Head 组件修改元数据

```js
import Head from 'next/head'
```

### css 样式

内置 `styled-jsx`

在 Next.js 中内置了 styled-jsx，它是一个 CSS-in-JS 库，允许在 React 组件中编写 CSS，CSS 仅作用与组件内部

```jsx
<Link href="/list">
	<a className="demo">jump to list page</a>
</Link>
<style jsx>{`
	.demo {
		color: red;
	}
`}</style>
```

还有一种方式是 css 模块

通过使用 CSS 模块功能，允许将组件的 CSS 样式编写在单独的 CSS 文件中

CSS 模块约定样式文件的名称必须为 “组件文件名称.module.css”

```jsx
// index.module.css
.p { color: green }

// index.js
import styles from './index.module.css'
<div className={styles.p}></div>
```

## 预渲染

预渲染是指数据和 HTML 的拼接在服务器端提前完成

预渲染可以使 SEO 更加友好

预渲染会带来更好的用户体验，可以无需运行 JavaScript 即可查看应用程序 UI

### 预渲染的两种方式

在 Next.js 中支持两种形式的预渲染：静态生成和服务端渲染

静态生成和服务端渲染是生成 HTML 的时机不同。

静态生成：静态生成是在构建时生成 HTML，以后的每次请求都共用构建时生成好的 HTML

服务器端渲染：服务器端渲染是在请求时生成 HTML，每个请求都会重新生成 HTML

Next.js 允许开发者为每个页面选择不同的预渲染方式，不同的预渲染方式拥有不同的特点，应根据场景进行渲染，但建议大多数页面建议使用静态生成

静态生成一次构建，反复使用，访问速度快，因为页面都是事先生成好的

适用场景：营销页面、播客文章、电子商务产品列表、帮助和文档

服务器端渲染访问速度不如静态生成快，但是由于每次请求都会重新渲染，所以使用数据频繁更新的页面或者页面随请求变化而变化的页面

### 无数据和有数据的静态生成

如果组件不需要在其它地方获取数据，直接进行静态生成

如果组件需要在其它地方获取数据，在构建时 Next.js 会预先获取组件需要的数据，然后再对组件进行静态生成

#### 静态生成 getStaticProps

getStaticProps 方法的作用是获取组件静态生成需要的数据，并通过 props 的方式将数据传递给组件，改方法是一个异步函数，需要在组件内部进行导出

在开发模式下，getStaticProps 改为在每个请求上运行

getStaticProps 就在打包的时候执行一遍，后面在生产环境是不会在执行的

```js
export async function getStaticProps() {
  // 从文件系统，API，数据库中获取的数据
  const data = ...
  // props 属性的值将会传递给组件
  return {
    props: ...
  }
}
```

这个代码虽然写在了组件里面，但是其实是在 node 环境里运行的

```js
import { readFile } from 'fs'
import { promisify } from 'util' 
import { join } from 'path'

const read = promisify(readFile)

export async function getStaticProps () {
  const data = await read(join(process.cwd(), 'pages', '_app.js'), 'utf-8')
  return {
    props: { // 这个值会作为组件的 props
      data
    }
  }
}
```

这个时候进行打包后的 html 里面是有 data

#### 服务器端渲染 getServerSideProps 

如果采用服务器端渲染，需要在组件中导出 getServerSideProps 方法

```js
export async function getServerSideProps (context) {
  // context 中会包含特定的请求参数
	return {
	  props: {
	  	// props for your component
	  }
	}
}
```

这个是服务器端渲染，所以每次去请求的时候，服务器都会执行 getServerSideProps 方法，因为每次都要重新构建 HTML，所以它每次构建的 HTML 都是不同的

在这里还有个问题就是上面我们写的服务端代码和客户端代码都混在了一起，这个其实会被 webpack tree shaking 掉，不用担心打包到了客户端的包里

### 基于动态路由的静态生成

基于参数为页面组件生成 HTML 页面，有多少参数生成多少 HTML 页面

在构建应用时，先获取用户可以访问的所有路由参数，再根据路由参数获取具体数据，然后根据数据生成静态 HTML

1. 创建基于动态路由的页面组件文件，命名时在文件名称外面加上 `[]`，比如 `[id].js`
2. 导出异步函数 `getStaticPaths`，用于获取所有用户可以访问的路由参数

```js
export async function getStaticPaths () {
	// 此处获取所有用户可以访问的路由参数
  return {
    // 返回固定格式的路由参数
    paths: [{params: {id: 1}}, {params: {id: 2}}],
    // 当用户访问的路由参数没有在当前的函数中返回时，是否显示 404 页面，false: 显示, true: 不显示
    fallback: false
  }
}
```

3. 导出异步函数 getStaticProps，用于根据路由参数获取具体的数据

```js
export async function getStaticProps({ params }) {
  // params -> { id: 1 }
  return {
    // 将数据传递到组件中进行静态页面的生成
    props: ...
  }
}
```

注意：getStaticPaths 和 getStaticProps 只运行在服务器端，永远不会运行在客户端，甚至不会被打包到客户端 JavaScript 中，意味着这里可以随意写服务器端代码，比如查询数据库

### fallback 的作用

前面讲到 `fallback` 为 false 的时候展示404，如果为 true，会重新执行 getStaticProps 拿到参数后生成 HTML 返回给用户

## 自定义 404 页面

要创建自定义 404 页面，需要在 pages 文件夹中创建 404.js 文件

```jsx
export default function Custom404 () {
  return <h1>404 - Page Not Found</h1>
}
```

## API Routes

什么是 API Routes

API Routes 可以理解为接口，客户人向服务端发送请求获取数据的接口

Next.js 应用允许 React 开发者编写服务器端代码创建数据接口

如何实现 API Routes

1. 在 pages/api 文件夹中创建 API Routes 文件，比如 user.js
2. 在文件中默认导出请求处理函数，函数有两个参数，req 为请求对象，res 为响应对象

```js
export default function (req, res) {
  res.status(200).send({ id: 1, name: 'Tom' })
}
```

注：当前 API Routes 可以接受任何 http 请求方法

3. 访问 API Routes: localhost:3000/api/user

不要再 getStaticPaths 或 getStaticProps 函数中访问 API Routes，因为这两个函数就是在服务器端运行的，可以直接写服务器端代码

# Gatsby

基于 React 的静态站点生成器

## 静态应用的优势

1. 访问速度快
2. 更利于 SEO 搜索引擎的内容抓取
3. 部署简单

## 总览

1. 基于 React 和 GraphQL，结合了 webpack，babel，react-router 等前端领域中最先进工具开发人员体验好
2. 采用数据层和 UI 层分离而不失 SEO 的现在前端开发模式，对 SEO 非常友好
3. 数据与读取，在浏览器空闲的时候预先读取链接对应的页面内容，是静态页面拥有 SPA 应用的用户体验，用户体验好
4. 数据来源多样化：Headless CMS，markdown，API
5. 功能插件化，Gatsby 中提供了丰富且功能强大的各类型插件，用什么装什么
