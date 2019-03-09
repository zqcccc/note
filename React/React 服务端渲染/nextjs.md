[demos 仓库](https://git.imweb.io/reactlearn/react-demos) 本章内容为第七章

nextjs 是非常强大的 react 的一站式服务端渲染框架，它可以和很多我们已知的框架相结合，具有很强的自定义的能力，同时也内置了很多 webpack、babel 的能力，既适合初学者的一站式部署，也适合高阶的一些玩法，它是很灵活的框架，在业界也是使用广泛

## 重构

重构之前的自定义服务端渲染的 `koa-server`

### html 模板变化

字符串模板 =》`jsx` 模板

### 编译方式变化

对 `js`、`less` 资源使用 `webpack` =》借助 `next` 以及插件编译资源

`Next.js` 内置了 `webpack` 的基本能力，默认情况下无需配置 `webpack`，省去了维护 `webpack` 的成本

### 数据管理变化

主动写入前段缓存 =》借助 `getInitialProps`

将原来的两步操作合并成一步操作，并且通过添加新“生命周期”的方式，来管理后端需要预拉取的数据，更方便管理

```js
class HackerNews extends React.Component {

    // ...
    
    static async getInitialProps({ req }) {
        const fetch = require('node-fetch');

        let response = await fetch('https://www.apiopen.top/journalismApi');
        const json = await response.json();

        return { typeLists: json.data };
    }
    
    // ...
    
}

```

## Next.js 高级功能

### 自定义 webpack

在 `next` 的配置文件即 `next.config.js` 中，我们可以对 `webpack` 进行定义，对 `webpack` 的配置是一个函数

```js
module.exports = {
    webpack: (config, { bulidId, dev, isServer, defaultLoaders }) => {
        // Perform customizations to webpack config
        // Important: return the modified config
        return config
    },
    webpackDevMiddleware: config => {
        // Perform customizations to webpack dev middleware config
        // Important: return the modified config
        return config
    }
} // 来自官方文档
```

### 不采用服务端渲染

有的组件不想通过服务端渲染，想做成动态加载的形式

```jsx
import dynamic from 'next/dynamic'

const DynamicComponentWithNoSSR = dynamic(() => import('../components/hello3'), {
  ssr: false
})

export default () => (
  <div>
    <Header />
    <DynamicComponentWithNoSSR />
    <p>HOME PAGE is here!</p>
  </div>
)
```

### 资源预拉取

`prefetch href=""` 就是预拉去的标志

```jsx
import Link from 'next/link'

// example header component
export default () => (
  <nav>
    <ul>
      <li>
        <Link prefetch href="/">
          <a>Home</a>
        </Link>
      </li>
      <li>
        <Link prefetch href="/about">
          <a>About</a>
        </Link>
      </li>
      <li>
        <Link prefetch href="/contact">
          <a>Contact</a>
        </Link>
      </li>
    </ul>
  </nav>
)
```

### 自定义

`next` 项目里的 `pages` 是默认的 react 目录，里面的每一个入口就是 `next` 所解析出来的每一个页面，这样默认的路由就是 `pages` 下的每个目录的名字，比较丑，所以我们还是要使用 `koa` 来转发路由

