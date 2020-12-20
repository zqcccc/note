# Gridsome 介绍

## 是什么

- GitHub 仓库：https://github.com/gridsome/gridsome
- 官网：https://gridsome.org/
- Gridsome 是由Vue.js驱动的Jamstack框架，用于构建默认情况下快速生成的静态生成的网站和应用。
  - 静态网站的好处
    - 省钱，不需要专业的服务器，只要能托管静态文件的空间即可
    - 快速，不经过后端服务器的处理，只传输内容
    - 安全，没有后端程序的执行，自然会更安全
- Gridsome是Vue提供支持的静态站点生成器，用于为任何无头CMS，本地文件或API构建可用于CDN的网站
- 使用Vue.js，webpack和Node.js等现代工具构建网站。通过npm进行热重载并访问任何软件包，并使用自动前缀在您喜欢的预处理器（如Sass或Less）中编写CSS。
- 基于 Vue.js 的 Jamstack 框架
- Gridsome 使开发人员可以轻松构建默认情况下快速生成的静态生成的网站和应用程序
- Gridsome允许在内容里面引用任何CMS或数据源。
  从WordPress，Contentful或任何其他无头CMS或API中提取数据，并在组件和页面中使用GraphQL访问它。

## 为什么选择 Gridsome

- **Vue.js for frontend** - The simplest & most approachable frontend framework.
- **Data sourcing** - Use any Headless CMSs, APIs or Markdown-files for data.
- **Local development with hot-reloading** - See code changes in real-time.
- **File-based page routing** - Any `Name.vue` file in `src/pages` is a static route.
- **Dynamic routing** - Any `[param].vue` file in `src/pages` is a dynamic route.
- **Static file generation** - Deploy securely to any CDN or static web host.
- **GraphQL data layer** - Simpler data management with a centralized data layer.
- **Automatic Code Splitting** - Builds ultra performance into every page.
- **Plugin ecosystem** - Find a plugin for any job.

## 什么是 Jamstack

Gridsome是一个Jamstack框架。 Jamstack使您可以通过预渲染文件并直接从CDN直接提供文件来构建快速安全的站点和应用程序，而无需管理或运行Web服务器。

[Learn more about the Jamstack](https://gridsome.org/docs/jamstack).

## 它是如何工作的

Gridsome生成静态HTML，一旦加载到浏览器中，该HTML就会渗入Vue SPA。这意味着您可以使用Gridsome构建静态网站和动态应用程序。

Gridsome为每个页面构建一个.html文件和一个.json文件。加载第一页后，它仅使用.json文件来预取和加载下一页的数据。它还为需要它的每个页面构建一个.js包（代码拆分）。

它使用vue-router进行SPA路由，并使用vue-meta来管理<head>。

Gridsome默认添加最小57kB的gzip JS捆绑包大小（vue.js，vue-router，vue-meta和一些用于图像延迟加载的文件）。

[详细了解其工作原理](https://gridsome.org/docs/how-it-works)

## 学习条件

您应该具有有关HTML，CSS，Vue.js以及如何使用终端的基本知识。了解GraphQL的工作原理是有好处的，但不是必需的。 Gridsome是学习它的好方法。

Gridsome 需要Node.js（v8.3 +），并建议使用 Yarn。

## 备选方案

- [VuePress](https://vuepress.vuejs.org/)
- [Nuxt](https://nuxtjs.org/)
- [Gatsby.js](https://www.gatsbyjs.org/)

## 使用场景

- 不适合管理系统
- 简单页面展示
- 想要有更好的 SEO
- 想要有更好的渲染性能