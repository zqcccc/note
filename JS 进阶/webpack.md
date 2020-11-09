# 前端打包

ES Modules 存在环境兼容问题，不能保证所有的用户的浏览器都是新的，模块文件过多，网络请求频繁，不光是 JS 所有的前端资源都需要模块化

- 将新特性的代码能转换成兼容性更好的代码
- 将散落的文件打包到一起，解决频繁请求的问题
- 支持不同种类的文件类型

## 模块打包工具概要

打包工具解决的是前端整体的模块化，并不单指 JavaScript 模块化

**Webpack**

- 模块打包器（Module bundle），将零散的文件打包到一起
- 模块加载器（Loader），将有兼容性问题的代码进行编译转换
- 代码拆分（Code Splitting），按照我们的需要去打包，不用把所有的代码到一个文件里，可以把初次运行中所必须的模块打包到一起，其他模块再单独存放，后面需要这些模块的时候再异步加载
- 资源模块（Asset Module），以模块的方式去载入任意类型的资源文件

## Webpack

webpack 4 以后支持零配置打包，默认 `src/index.js` 作为入口打包到 `dist/main.js`，大多数场景都是要自己配置 webpack 的，在项目根目录下创建 `webpack.config.js` ，这是个运行在 node 环境下的文件，我们需要按照 CommonJS 的方式去编写代码

```js
const path = require('path')

module.exports = {
  entry: './src/main.js', // 相对路径 ./ 不能省略
  output: {
    filename: 'bundle.js', // 生成目标文件名
    path: path.join(__dirname, 'output') // 生成路径要用绝对路径
  }
}
```

### 工作模式

webpack 4 中新增了工作模式的用法，大大简化了 webpack 的配置复杂程度，可以理解成针对不同环境的几组预设的配置

如果没有设置，默认就是 `production` 模式，压缩代码

`yarn webpack --mode development` 开发模式下会优化打包的速度，会添加一些调试过程中的辅助代码

`none` 就是最原始的打包，不会做额外的处理

https://webpack.js.org/configuration/mode/

除了在 cli 中去指定模式，在配置文件中也可以指定

webpack 打包后的代码并不会特别复杂，它帮我们把所有的模块代码放到了同一个文件当中，并提供一些基础代码，让模块与模块之间相互依赖的关系能保持原有的状态，这就是打包完的 *webpackBootstrap* 函数的作用

### 资源模块加载

webpack 不光是 JavaScript 的模块化打包工具，它也是整个（前端项目）前端工程的模块打包工具，我们可以通过 webpack 去引入前端的任意类型文件

webpack 内部默认只会去处理 JavaScript 文件，会把打包过程中遇到的所有文件都当初 JavaScript 去解析，去直接处理 css 会报错的，我们需要恰当的 loader 去加载我们对应文件类型的文件

然后在配置文件中添加 `module` 对象，`module` 下添加 `rules` 数组，数组的每个对象都是我们针对其他类型资源模块的加载规则的配置，每个规则都要设置两个属性，首先是 `test` 属性，它是正则表达式，用来去匹配在打包过程中遇到的文件路径，`use` 属性用来匹配到的文件需要去使用的 loader

css-loader 的作用就是将我们的 css 文件转换成一个 js 模块，具体的实现就是将我们的 css 代码 push 到一个数组当中，这个数组是由 css-loader 内部的一个模块提供的，整个过程中还没有用到这个数组，所以还需要一个 loader 叫 style-loader，作用就是把 css-loader 转换后的结果通过 style 标签的方式追加到页面上

`use` 属性被修改成一个数组，将 style-loader 也放进去，数组里如果有多个 loader 就会从后往前执行

Loader 是 webpack 实现整个前端模块化的核心，借助于 Loader 就可以加载任何类型的资源

确实可以把 css 当成打包入口，但是一般还是将 js 作为打包入口，打包入口一般也是运行入口，JavaScript 驱动整个前端应用的业务，然后在 js 中去引入其他类型的资源

可能有一个问题，传统的做法都是将样式和 js 分离开，单独去维护，单独去引入，而 webpack 又建议我们在 js 中去引入 css，其实 webpack 不光建议我们在 js 中去引入 css，而是根据我们写的代码去引入任何需要的资源文件，需要资源的不是应用，而是代码，是代码要正常的工作必需要引入资源，这就是 webpack 的哲学，通过 JavaScript 驱动整个前端应用

- 逻辑合理，JS 确实需要这些资源文件
- 确保上线资源部缺失，都是必要的

### 文件资源加载器

目前 webpack 社区提供了非常多的加载器，基本上所有合理的需求都会有对应的 loader，有些资源可以变成 js 中的代码，比如 css，但有的资源是不能用 js 去表示的，比如说图片和字体

这里要讲到 file-loader

```js
module.exports = {
  // ...
  output: {
    // ...
    publicPath: 'dist/', // 配置后才能访问到正确的路径，不能省略 /
  },
  module: {
    rules: [
      // ...
      {
        test: /.png$/,
        use: 'file-loader'
      }
    ]
  }
}
```

### URL 加载器

Data URLs 与 url-loader，Data URLs 是一个特殊的 url 协议，可以用来直接去表示一个文件，传统的 url 要求服务器上有对应的文件，我们通过请求这个地址得到对应的文件，而 Data URLs 是 url 就可以直接去表示文件内容的方式，url 的文本就包含的文件的内容，在使用这种 url 的时候就不会再去发出 http 请求

`data:[<mediatype>][;base64],<data>`

`协议:[<媒体类型>][;编码],<文件内容>`

比如这个 Data URLs `data:text/html;charset=UTF-8,<h1>html content</h1>`

加载这样的 url 在 webpack 中就需要用到 url-loader 了

```js
module.exports = {
  // ...
  module: {
    rules: [
      // ...
      {
        test: /.png$/,
        use: 'url-loader' // 如果直接打包的话，所生成的文件就不会有 png 文件了，图片就变成了 Data URLs
      }
    ]
  }
}
```

这种方式很适合体积比较小的资源，如果体积很大的话就会造成打包结果很大，从而影响速度

最佳实践应该是

- 小文件使用  Data URLs，减少请求次数
- 大文件单独提取存放，提高加载速度

```js
module.exports = {
  // ...
  module: {
    rules: [
      // ...
      {
        test: /.png$/,
        use: {
          loader: 'url-loader',
          options: {
            limit: 10 * 1024 // 10KB 以下是 url-loader，超过就是 file-loader，所以如果是这样使用，一定也要装 file-loader，即使好像没有显式地配置
          }
        }
      }
    ]
  }
}
```

### 常用加载器分类

大致分为三类

- 编译转换类
  - 会将加载到的资源模块转换为 JavaScript 代码，比如 css-loader
- 文件操作类
  - 一般是把加载的文件拷贝到输出目录，将文件的访问路径向外导出，比如 file-loader
- 代码检查类
  - 统一代码风格，提高代码质量，比如 eslint-loader，这种加载器一般不会去修改生产环境的代码

### webpack 与 ES2015

由于 webpack 默认加能处理 import export，所以很容易就认为 webpack 会编译 ES6 的代码，实际上不是的，webpack 仅仅是对模块去完成打包工作，所以才会对 import 和 export 作相应的转换，它并不能去转换代码中其他的 ES6 的特性

转换新特性用的最多的还是 babel-loader

`yarn add bebel-loader @babel/core @babel/preset-env --dev`

```js
module.exports = {
	module: {
		rules: [
			{
				test: /.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
			}
		]
	}
}
```

- webpack 只是打包工具，不会去转换 es6 或者更高的新特性
- 加载器可以用来编译转换代码
- babel 是代码转换的平台，具体怎么转换还是需要配置一下

### 模块加载方式

- 遵循 ES Modules 标准的 import 声明
- 遵循 CommonJS 标准的 require 函数
  - 如果是通过 require 引入一个 ES Module 的话，`require('xxx').default` 才能拿到 ES Module 的默认导出
- 遵循 AMD 标准的 define 函数和 require 函数

除非必要的情况，不然就不要再项目中去混合使用这些标准，那样会大大降低项目的可维护性，每个项目统一地去使用一种就行了

Loader 加载的非 JavaScript 也会触发资源加载，比如

- 样式代码中的 `@import` 指令和 url 函数
- HTML 代码中图片标签的 src 属性

如果在 css 中用了 `background-image: url(xxxx.png)` 的这种就会让 url-loader 去处理，`@import url(xxx.css)` 同理

如果是在处理 html 中的资源就要用到 html-loader，然后 img 标签的 src 属性就会被加载处理打包，如果有个 a 标签的 href 指向了一个资源，需要去打包的话，可以配置 html-loader

```js
module.exports = {
  module: {
    rules: [
      {
        test: /.html$/,
        use: {
          loader: 'html-loader',
          options: {
            attrs: ['img:src', 'a:href'] // 额外配置需要去打包的资源
          }
        }
      }
    ]
  }
}
```

### 核心原理

在一个普通的前端项目中，会散落着各种各样的代码及资源文件，webpack 会根据我们的配置找到其中的一个文件作为入口，一般入口文件会是一个 JavaScript 文件，然后顺着入口文件的代码，根据模块引入的语句，然后解析推断这个入口文件需要的资源模块，然后再去解析资源模块对应的依赖，然后得到整个项目的依赖树，然后 webpack 会递归这个依赖树，找到每个节点需要的资源文件，最后根据 rules 找到文件对应的加载器，将文件交给加载器去加载这个模块，最后会把加载的结果放到 bundle.js，也就是打包结果当中，从而实现整个项目的打包

在上述的整个过程当中，Loader 起了很重要的作用，因为没有 Loader 的话就没办法实现各种各样资源的加载，那 webpack 就是只能打包合并 js 代码的工具了

### Loader 的工作原理

每个 loader 都是导出一个函数，这个函数就是这个 loader 对加载到的资源的处理过程，函数的入参就是加载到资源文件的内容，函数返回值就是函数处理完的结果，在配置 `module.rules 规则` 对象 `use` 的时候，可以当成 `require`，除了加载第三方模块，加载文件路径也是可以的

Load 文件的过程有点像是一个工作管道，这个过程中可以依次去使用多个 loader，最终这个管道处理过后的结果必须是 JavaScript 代码字符串

```js
const marked = require('marked')

module.exports = source => {
  // console.log(source)
  // return 'console.log("hello ~")'
  const html = marked(source)
  // return html
  // return `module.exports = "${html}"` // 引号和换行符有可能造成错误，所以要用 JSON.stringify
  // return `export default ${JSON.stringify(html)}` // 也可以用 ES Module 的方式去导出，webpack 会自动转换 ES Module 导出的代码

  // 返回 html 字符串交给下一个 loader 处理
  return html
}
```

### 插件机制

Loader 专注实现资源模块加载，Plugin 解决其他自动化工作

e.g. 清除 dist 目录；拷贝静态文件至输出目录；压缩输出代码

webpack + plugin 实现大多前端工程化工作

#### 自动清除输出目录

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
	// ...
  plugins: [
    new CleanWebpackPlugin()
  ]
}

```

#### 自动生成使用 bundle.js 的 HTML

之前的 HTML 都是通过硬编码的方式单独去存放在项目根目录下的，会有以下问题

- 发布是需要同时去发布项目根目录下的 HTML 和 dist 目录下所有的打包结果，上线后 HTML 中的路径引用需要确认都是正确的
- 如果输出的目录或者文件名（配置）发生了变化，那 HTML 中 script 所引用的路径也需要手动去修改

解决上面问题的方法最好是通过 webpack 输出 HTML 文件，让 HTML 也参与到构建过程，在构建过程中，webpack 知道它生成了多少个 bundle，它会自动将这些打包的 bundle 添加到页面当中，这样 HTML 也输出到 dist 目录，HTML 对 bundle 的引用也是动态的注入的，不需要人为的手动硬编码，确保路径的引用是正常的

`yarn add html-webpack-plugin --dev` 

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  // ...
  plugins: [
    // 用于生成 index.html
    new HtmlWebpackPlugin({
      title: 'Webpack Plugin Sample',
      meta: {
        viewport: 'width=device-width'
      },
      template: './src/index.html'
    }),
    // 用于生成 about.html
    new HtmlWebpackPlugin({
      filename: 'about.html'
    })
  ]
}

```

#### copy-webpack-plugin

在项目中一般也有不需要参与构建的静态文件，它们也需要去发布到线上，比如 `favicon.ico`，这种情况下就需要 copy-webapck-plugin

以上都是比较常见的 webpack 插件，社区中还提供了很多其它插件，不需要每个都认识，在又特殊需求的时候去提炼关键词搜索就行了

### 插件原理

相比于 Loader，Plugin 拥有更宽的能力范围，因为 Loader 只是在加载模块的环节去工作，而 plugin 几乎可以触及到 webpack 工作的每一个环节

Plugin 的实现机制其实就是在软件开发过程中最常见的钩子机制，在 webpack 的工作过程中会有很多的环节，为了方便 plugin 的拓展，webpack 几乎给每个环节都埋下了钩子，这样在开发 plugin 的时候，就可以往不同的节点上去挂载任务从而拓展 webpack 的能力

https://webpack.js.org/api/compiler-hooks/

webpack 要求 plugin 必须是一个函数或者是一个包含 apply 方法的对象，一般都会定义一个类，然后在中去实现 apply 方法，使用的时候就是用类去实例化生成一个实例去使用

```js
// 加入要去掉 bundle.js 里的注释
class MyPlugin {
  apply (compiler) {
    console.log('MyPlugin 启动')

    compiler.hooks.emit.tap('MyPlugin', compilation => {
      // compilation => 可以理解为此次打包的上下文
      for (const name in compilation.assets) {
        // console.log(name)
        // console.log(compilation.assets[name].source())
        if (name.endsWith('.js')) {
          const contents = compilation.assets[name].source()
          const withoutComments = contents.replace(/\/\*\*+\*\//g, '')
          compilation.assets[name] = {
            source: () => withoutComments,
            size: () => withoutComments.length
          }
        }
      }
    })
  }
}

module.exports = {
  // ...
  plugins: [
    // ...
    new MyPlugin()
  ]
}
```

### 开发体验问题

到现在的开发流程是

1. 编写源代码
2. webpack 打包
3. 运行应用
4. 刷新浏览器

开发方式过于原始

设想理想的开发环境

1. 以 HTTP Server 运行
2. 自动编译 + 自动刷新
3. 提供 Souce Map 支持

#### 自动编译

通过 webpack-cli 提供的 watch 工作模式，项目下的源文件会被监听，自动重新打包

`yarn webpack --watch`

#### 自动刷新浏览器

希望编译过后自动刷新浏览器，BrowserSync 工具就能帮我们实现这个功能

`yarn browser-sync dist --files "**/*"`

其实这种方式也有弊端

- 操作上麻烦，需要去操作两个工具
- 效率上降低了，webpack 会将文件不断写入磁盘，browser-sync 再从磁盘中读出来

### Webpack Dev Server

Webpack Dev Server 提供用于开发的 HTTP Server，集成「自动编译」和「自动刷新浏览器」等功能，用这个工具就能解决我们前面遇到的问题，因为这是个高度集成的工具，所以使用起来比较简单

`yarn add webpack-dev-server --dev`

安装这个模块后，在 node_modules 中的 `.bin` 目录下就有了 webpack-dev-server 的 cli 程序，我们就可以用 yarn 去运行这个 cli，或者定义到 npm scripts 中

运行 `yarn webpack-dev-server` 后就会自动地去打包这个应用，并且会启动一个 http server 去运行我们的打包结果，在运行后还会去监听我们的代码变化，一旦项目的源文件代码发生变化，它就会自动打包，这跟 watch 模式是一样的，webpack-dev-server 为了提高工作效率，并没有将打包结果写入到磁盘当中，它是将打包结果暂时存在内存当中，而 http server 就是从内存中将这些打包结果读出来，然后发送给浏览器，这样就减少了上面两次多余的磁盘读写操作，大大提高构建效率

`yarn webpack-dev-server --open` 打包后自动唤起浏览器并监视文件变化

#### 静态资源访问

webpack-dev-server 默认只会 server 打包输出文件，通过 webpack 打包输出的文件都可以被访问到，但是还有一些静态资源也需要作为开发资源被访问的话，需要额外的告诉 webpack-dev-server

```js
module.exports = {
  // ...
  devServer: { // 专门配置 webpack-dev-server
    contentBase: './public', // 也可以是路径字符串数组
  },
  plugins: [
    // ...
    // 这里配置的静态资源拷贝，只在上线前的那次打包中使用，开发阶段最好不要使用这个插件，因为在开发过程中会频繁执行打包任务，如果拷贝文件比较多或者比较大，那每次打包的时候都会去执行这个插件的任务，这样的开销就会很大
    // new CopyWebpackPlugin(['public'])
  ]
}
```

#### 代理 API 服务

由于是开发服务器，所以我们开发的时候应用会运行在 localhost 的一个端口上，而一般上线后，应用又会和 API 一起部署在同源地址下 e.g. https://www.example.com/

这有个问题就是，在实际生产环境中，前端应用可以直接去访问 api，但是在开发环境中就会产生跨域请求问题，我们可以通过跨域资源共享（CORS）解决，使用 CORS 的前提是 API 必须支持，但不是任何情况下 API 都应该支持 CORS，如果前后端同源部署的话，这种情况下根本没有必要去开启 CORS，这个开发阶段的跨域问题也很常见

最好的解决方法是在开发服务器中去配置代理服务，就是将接口服务代理到本地的开发服务地址，webpack-dev-server 就支持配置开启代理

假如我们现在要将 GitHub API 代理到开发服务器

```js
module.exports = {
  // ...
  devServer: {
    proxy: { // 服务代理配置
      '/api': { // 需要代理的请求路径前缀
        // http://localhost:8080/api/users -> https://api.github.com/api/users
        target: 'https://api.github.com',
        // http://localhost:8080/api/users -> https://api.github.com/users
        pathRewrite: {
          '^/api': ''
        },
        // 不能使用 localhost:8080 作为请求 GitHub 的主机名，主机名是 HTTP 协议中的相关概念
        changeOrigin: true
      }
    }
  },
}
```

### Source Map

通过构建编译之类的操作我们可以将开发阶段的源代码转换成可以在生产环境中运行的代码，运行代码与源代码之间完全不同，如果应用出现了错误需要调试应用，调试和报错都是基于运行代码，错误信息无法定位

Source Map 是解决这类问题的最好办法，源代码地图，它就是用来映射转换过后的代码和源代码的一个关系

一段转换过的代码通过 Source Map 逆向解析可以得到源代码，Source Map 文件以 `.map` 结尾，其实是一个 json 文件，里面记录的就是转换过后的代码和源代码的映射关系

一般有以下字段：

- version，表明 Source Map 标准版本
- sources，记录的就是转换之前源文件的名称，可能是多个文件转换成了一个文件，所以这里是字符串数组
- names，源代码中使用的成员名称
  - 在压缩代码时，会将开发阶段那些有意义的变量名变换成一些简短的字符，从而压缩整体代码的体积，这个 names 就是记录的原始变量的名称
- mappings，这是 Source Map 的核心属性，这是一个 base64-vlq 编码的字符串，它记录的就是我们转换过后的代码中的字符与转换之前的所对应的映射关系

一般有了 Source Map 这样的一个文件后，会在转换过后的文件中以注释的方式引入这个 Source Map 文件，Source Map 其实只是开发人员在开发阶段用来调试代码和定位错误的，其实对生产环境没有什么用

在最新版的 jQuery 中已经去除了 Source Map 的注释，如果想要尝试的话可以手动的添加上注释，在 `jquery-x.x.x.min.js` 文件的最后一行，添加 `//# sourceMappingURL=jquery-x.x.x.min.map`

在浏览器当中，如果打开了开发人员工具的话，那开发人员工具加载到这个 JS 文件最后有 Source Map 的注释，浏览器就是自动去请求这个 Source Map 文件，然后根据 Source Map 逆向解析出来源代码以便于调试和定位错误

Source Map 解决了源代码与运行代码不一致所产生的调试问题

#### webpack 配置 Source Map

webpack 也支持对打包出来的 bundle 生成对应的 Source Map 文件，但它提供了很多模式所以导致初学者不太容易理解

webpack 支持 12 种不同的方式，每种方式的效率和效果各不相同，效果最好的一般也最慢，速度最快的一般也没什么效果

https://webpack.js.org/configuration/devtool/

webpack Source Map 有个 eval 模式，在 JS 中也有个 eval 函数，可以运行字符串中的 JS 代码，默认这些代码会运行在浏览器中临时的虚拟机环境中，比如 `eval(console.log(123))`，可以通过 `sourceURL` 来声明这段代码所属文件的路径，即 `eval(console.log(123) //# sourceURL=./foo/bar.js)`，这样就可以表示这段运行的代码就是来自 ./foo/bar.js，其实执行还是在虚拟引擎，但是开发者可以点击跳转到源文件

```js
module.exports = {
  // ...
  devtool: 'eval',
}
```

webpack 设置成 eval 模式后，在浏览器如果报错的话，就能根据错误信息找到出错的文件，但是跳转打开的缺失打包过后的模块代码，因为这种模式会将每个模块的转换过后的代码都放在 eval 函数中去执行，并在 eval 函数执行的字符串最后通过 sourceURL 去说明对应的文件路径，这样浏览器在通过 eval 在执行这段代码的时候就知道其所对应的源代码的文件是哪个，这种方式只能定位文件，其实这种模式不会生成 source map 文件，跟 source map 没什么太大关系，构建速度最快，效果就很简单，只能定位源代码的文件路径，但是不知道具体的行列信息

#### 不同 devtool 的差异

在比较不同的 devtool 的差异的时候去得使用多个 webpack 去打包，webpack 导出的配置可以是一个数组，数组中的每一个对象都是单独的打包配置，这样就可以在一次打包过程中去执行多个打包任务

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

const allModes = [
	'eval',
	'cheap-eval-source-map',
	'cheap-module-eval-source-map',
	'eval-source-map',
	'cheap-source-map',
	'cheap-module-source-map',
	'inline-cheap-source-map',
	'inline-cheap-module-source-map',
	'source-map',
	'inline-source-map',
	'hidden-source-map',
	'nosources-source-map'
]

module.exports = allModes.map(item => {
	return {
		devtool: item,
		mode: 'none', // 不做其他额外的处理
		entry: './src/main.js', // 打包之前会在源文件里故意写一个错误
		output: {
			filename: `js/${item}.js` // 在 dist 下 serve 后不用看 js，直接点 HTML 看就可以了，因为 plugin 已经配置了生成 html
		},
		module: {
			rules: [
				{
					test: /\.js$/,
					use: {
						loader: 'babel-loader', // 这个 loader 是为了其中的一个模式准备的
						options: {
							presets: ['@babel/preset-env']
						}
					}
				}
			]
		},
		plugins: [
			new HtmlWebpackPlugin({
				filename: `${item}.html`
			})
		]
	}
})
```

下面写的这些 source map 配置名称和最新的 webpack devtool 配置可以会有些不太一样了

eval 只定位出错文件路径

eval-source-map 可以定位到行列

cheap-eval-source-map 只能定位到行

cheap-module-eval-source-map 也是能定位行，和 cheap-eval-source-map 相比就是可以看到经过 babel 转换之前的代码

了解上面的几种模式其实也差不多了，别的模式都是组合下面的这些

- eval - 是否使用 eval 执行模块代码
- cheap - Source Map 是否包含行信息
- module - 是否能够得到 Loader 处理之前的源代码

inline-source-map 和 source-map 的区别是，source-map 最后的注释指向的另一个 source map 文件，inline-source-map 则是以 data url 的方式将 source map 嵌入到代码当中，这几乎不会用的，因为中方式生成的文件体积都很大

hidden-source-map 就是会生成 source map 文件，但是在运行的代码中不会去引用 source map 文件，比如前面讲到的 jQuery 最新版，一般是在开发一些第三方包的时候会用这个模式，source map 其实还可以用 http 的方式去引入的

nosources-source-map，能看到出现的错误的位置，但是点击跳转时看不到源代码的，这种模式就是提供了错误的行列信息，但是在浏览器的开发这工具中是看不到这个源代码而已，为的是在生产环境中去保护源代码不暴露

那有这么多的 sourcemap 配置，应该怎么选最合适的呢？

开发模式 cheap-module-eval-source-map

- 代码每行不超过 80 个字符，定位到行就够了
- 使用框架的情况会很多，代码经过 Loader 转换过后的差异较大，所以要有 module
- 首次打包速度慢无所谓，重写打包相对较快

生产模式 none

sourcemap 会暴露源代码，调试应该是开发阶段的事，最多用 nosources-source-map

选择没有绝对，选择合适自己的才是最好的

### 自动刷新

webpack-dev-server 提供对开发者友好的开发服务器，使用 webpack-dev-server 可以让我们更专注于编码，但还是有的场景是不太舒服的

比如现在有个 input 框，我们在里面输入了一些文本看看效果，然后修改了代码后，页面刷新，原来输入框中的内容就被清除了，这不是我们想要的，这种自动刷新好像也不是很好用，因为刷新就把页面的状态给丢失了

方法1：代码中写死编辑器的内容

方法2：额外代码实现刷新前保存，刷新后读取

这些方法还是不能解决根本问题，页面不刷新的前提下，模块也能及时更新

#### HMR 模块热替换

Hot Module Replacement，计算机中有热拔插的概念，指的就是在一个正在运行的机器上随时插拔设备，机器的运行状态不会受插拔设备的影响，插上的设备可以立即开始工作，模块热替换也是在应用运行过程中的实时替换某个模块，应用运行状态不受影响

自动刷新导致页面状态丢失，热替换只将修改的模块实时替换至应用中

HMR 是 webpack 中最强大的功能之一，极大程度的提高了开发者的工作效率

HMR 已经集成在 webpack-dev-server 中，`yarn webpack-dev-server --hot`，也可以通过配置文件开启

```js
const webpack = require('webpack')
module.exports = {
  // ...
  devServer: {
    hot: true
    // hotOnly: true // 只使用 HMR，不会 fallback 到 live reloading
  },
  plugins: [
    // ...
    new webpack.HotModuleReplacementPlugin()
  ]
}
```

照上面那样配置之后，css 好像可以热更新，但是 js 还不可以

webpack 中的 HMR 并不可以开箱即用，webpack 中的 HMR 需要手动处理模块热替换逻辑

Q1. 为什么样式文件实现了热更新可是也没有手动去写逻辑呢？因为样式文件的 Loader 处理了热更新的逻辑

Q2. 那为什么样式文件就可以自动处理，js 文件就不可以呢？因为样式模块更新后，它只需要把更新后的 css 即时替换到页面当中，新的样式就会覆盖老的样式，从而实现热更新，但是 JavaScript 代码的编写是没有任何规律的，你可以在一个模块中导出的是一个对象，也可以是一个字符串或者函数，对导出的成员的是用也是各不相同的，所有面对毫无规律的 JavaScript 模块就不知道怎么去处理更新后的模块，那就无法去实现一个通用的模块替换方案

Q3. 为什么我的项目没有手动处理，JS 照样可以热替换？可能是因为你使用的是框架，框架下的开发，每种文件都是有规律的，通过脚手架创建的项目内部都集成了 HMR 方案

总结：我们需要手动处理 JS 模块更新后的热替换

HotModuleReplacementPlugin 为 js 提供了处理 HMR 的 API，在自己的代码中去使用这套 API 去处理当某一个模块更新后应该如何替换掉当前正在运行的页面当中

假设项目的入口文件是 main.js，它引用了一些模块，如果这些模块更新了后，那 main.js 就要重新使用这些模块，所以需要在 main.js 处理更新模块的热替换

api 给 module 提供了 hot 属性，这个属性也是一个对象，这是 HMR 的核心对象

```js
import createEditor from './editor'
import background from './better.png'
import './global.css'

const editor = createEditor()
document.body.appendChild(editor)

const img = new Image()
img.src = background
document.body.appendChild(img)

// ============ 以下用于处理 HMR，与业务代码无关 ============

if (module.hot) {
  let lastEditor = editor
  module.hot.accept('./editor', () => {
    const value = lastEditor.innerHTML // 这里 input 的状态需要单独写逻辑保存，webpack 没法写通用的热更新逻辑
    document.body.removeChild(lastEditor)
    const newEditor = createEditor()
    newEditor.innerHTML = value
    document.body.appendChild(newEditor)
    lastEditor = newEditor
  })

  module.hot.accept('./better.png', () => {
    img.src = background
    console.log(background)
  })
}
```

热更新需要写单独的逻辑可能比较麻烦，但感觉还是利大于弊，就跟单元测试一样，对于一个长期开发的项目，这点额外的工作并不算什么，如果代码能更优规律点的话，可以实现一些通用的方案，如果用的是框架的话，HMR 将十分简单，因为框架都有成熟的 HMR 方案，上面的是纯原生，使用相对麻烦，这也可能是大部分人喜欢集成式框架的原因，因为足够简单

#### 注意事项

1. 处理 HMR 的代码报错会导致自动刷新

如果热替换报错的就会自动刷新页面，看不到报错信息。解决方法就是使用 hotOnly，这样热更新不管成功不成功都不会自动刷新

2. 没启用 HMR 的情况下，HMR API 报错

先判断 `module.hot` 是否存在

3. 代码中多了一些与业务无关的代码

在 webpack 打包后，和热替换相关的代码都被去除掉了，其实不会影响到生产环境

### 生产环境优化

学会了前面的那些用法和特性后，我们有了更好的开发体验，体验提升的同时，打包结果也越来越臃肿，webpack 在这个过程当中为了实现那些特性，会自动往打包的结果中添加一些额外的内容，比如 sourcemap 和 hmr，这些额外的代码对生产环境来说是冗余的，生产环境和开发环境有着很大的差异，生产环境注重运行效率，开发环境注重开发效率，针对这个问题，webpack 4 推出了模式 mode 的配置，在 production 模式下就做了很多生产环境需要的配置，webpack 也建议我们为不同的工作环境创建不同的配置以便于我们的打包结果可以适用于不同的环境

#### 不同环境下的配置

1. 配置文件根据环境的不同导出不同的配置
2. 一个环境对应一个配置文件

webpack 配置文件还支持导出一个函数，在函数中去返回我们需要的配置对象

```js
const webpack = require('webpack')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')

module.exports = (env, argv) => {
  const config = {
    // ...
  }

  if (env === 'production') {
    config.mode = 'production'
    config.devtool = false
    config.plugins = [
      ...config.plugins,
      new CleanWebpackPlugin(),
      new CopyWebpackPlugin(['public'])
    ]
  }

  return config
}
```

在全局去判断环境变量然后去导出相应的配置也是可以的

#### 不同环境下的多配置文件

根据环境变量然后去导出不同的配置适合中小型项目，一旦项目变得复杂，配置也会变得复杂起来，对于大型项目还是不同的环境对应不同的配置文件比较好，这种多配置文件的情况下，一般会有三个配置文件，其中两个是来适配不同的环境，另外一个是公共配置，因为开发环境配置和生产环境配置不是完全不同，所以需要一个公共的文件来抽象两者相同的配置

在项目根目录下创建 `webapck.common.js`、`webpack.dev.js`、`webpack.prod.js` 后

在 `webpack.dev.js`、`webpack.prod.js` 里合并 `webapck.common.js` 的配置，Object.assign 不太合适，因为在合并公共 plugins 这种引用类型的时候，会完全覆盖原来的 plugins 数组，lodash.merge 好像也可以，但社区有更好的方案，就是 `webapck-merge`，先安装开发依赖 `yarn add webpack-merge --dev`

```js
const merge = require('webpack-merge') // 社区提供的专门用来合并配置的函数
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const CopyWebpackPlugin = require('copy-webpack-plugin')
const common = require('./webpack.common') // 公共配置

module.exports = merge(common, {
  mode: 'production',
  plugins: [
    new CleanWebpackPlugin(),
    new CopyWebpackPlugin(['public'])
  ]
})
```

配置文件写好了后，因为现在的配置文件已经不是 webpack 默认的文件名，就需要去指定配置文件 `yarn webpack --config webpack.prod.js`，这样的命令比较复杂，可以写到 npm scripts 中去

### DefinePlugin

在 webpack 新增的 production 模式下，内部就开启了很多通用的优化功能，这种开箱即用的体验是很方便的，但对于学习来说，开箱即用很容易导致我们忽略到很多需要了解的内容，以至于出现问题后我们无从下手，如果需要深入了解 webpack 的使用，建议单独去了解 webpack 每个配置项的作用，这里先讲一些优化配置

插件 DefinePlugin 是内置插件，为代码注入全局成员，在 production 模式下，这个插件默认就是启用，并往代码中注入了 `process.env.NODE_ENV` 的常量，很多第三方的模块都是通过这个成员去判断当前运行的环境，从而决定是否去执行例如打印日志这种操作

DefinePlugin 是内置插件，需要先导入 webpack 模块，`webpack.DefinePlugin` 接受一个对象，对象的所有键值都会直接注入代码中，值是一个代码片段，是一段 JS 语法的代码的字符串，如果想要直接注入一个字符串，就要用 `JSON.stringify` 处理一下字符串就好

```js
const webpack = require('webpack')
module.exports = {
  mode: 'none',
  entry: './src/main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.DefinePlugin({
      // 值要求的是一个代码片段
      API_BASE_URL: JSON.stringify('https://api.example.com')
    })
  ]
}
```

### Tree-shaking 和 Scope Hoisting

字面意思就是摇树，一般伴随摇树这样一个动作，树上的枯树枝和树叶就会掉落下来，Tree-shaking 也是相同的道理，不过在这里摇掉的是代码中没有未引用的部分，即未引用代码（dead-code）

Webpack 生产模式优化下就有有用这个功能，自动检测代码中未引用的代码，然后移除它们

webpack 在生产模式下也是自动去开启这个功能的

Tree Shaking 不是指某个配置选项，它是一组功能搭配使用后的优化效果，production 模式下自动启用，在官方的 Tree-shaking 介绍有点混乱，下面就要在其它模式下手动去开启 Tree-shaking，这样可以了解 Tree-shaking 的工作工程，以及一些其它的优化功能

```js
module.exports = {
  mode: 'none',
  // 其他配置已省略...
  optimization: { // 集中去配置 webpack 中的优化功能
    // 模块只导出被使用的成员
    usedExports: true, // 负责标记「枯树叶」
    // 压缩输出结果，将未引用的代码去除
    // minimize: true // 负责将「枯树叶」摇掉

    // 尽可能合并每一个模块到一个函数中
    concatenateModules: true, // 及提升了运行效率，又减少了代码的体积，也叫 Scope Hoisting，是在 webpack3 添加的特性
  }
}
```

#### Tree-shaking & Babel

由于早期 webpack 发展特别快，变化也比较多，所以我们找资料的结果并不一定适用于当前所使用的的版本，对 Tree-shaking 的资料更是如此，很多资料显示如果使用了 babel-loader，就会导致 Tree-shaking 失效

Tree-shaking 前提是 ES Modules，由 Webpack 打包的代码必须使用 ESM，为了转换代码中的 ECMAScript 的新特性，很多时候就会选择 babel-loader 去处理 JS，而 babel 处理就会处理掉 ESM，转换成 CommonJS，一般也用的就是 `@babel/preset-env`，这个里面就有转换 ESM 的插件，这样在打包的时候，ESM 就都被转成了 CommonJS 规范的代码，这样 Tree-shaking 就不能生效了

但其实最新版本的 babel-loader 中已经自动关闭的转换 ESM 的插件，具体查看 babel-loader 和 preset-env 源码就知道了，那经过 babel 处理的代码还是 ESM，那就可以正常 Tree-shaking

其实也可以在 bebel preset 中强制去开启，不过给 preset-env 添加配置的方式很特别，很容易配错，需要数组套数组

```js
module.exports = {
  mode: 'none',
  entry: './src/index.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              // 如果 Babel 加载模块时已经转换了 ESM，则会导致 Tree Shaking 失效
              // ['@babel/preset-env', { modules: 'commonjs' }]
              // ['@babel/preset-env', { modules: false }]
              // 也可以使用默认配置，也就是 auto，这样 babel-loader 会自动关闭 ESM 转换
              ['@babel/preset-env', { modules: 'auto' }]
            ]
          }
        }
      }
    ]
  },
  optimization: {
    // 模块只导出被使用的成员
    usedExports: true,
    // 尽可能合并每一个模块到一个函数中
    // concatenateModules: true,
    // 压缩输出结果
    // minimize: true
  }
}
```

### sideEffects

webpack 4 中新增 sideEffects 的新特性，允许以配置的方式去标识代码是否有副作用，从而为 Tree shaking 提供更大的压缩空间

副作用：模块执行时除了导出成员之外所做的事情

sideEffects 特性一般在我们开发一个 npm 模块的时候才会用到，一般用于 npm 包标记是否有副作用，官网中把 sideEffects 和 Tree-shaking 的介绍混在一起，所以会有人认为它俩有因果关系，其实它俩没有很大的关系

假如在一个文件夹下有很多同类型的组件，然后由 index.js 集中来导出，别的模块通过 这个 index.js 引用了其中一个组件，这就造成了 webpack 误认为有副作用导致其他模块也被打包进去的情况。

我们可以在前面讲到的的 optimization 属性中开启 sideEffects 为 true 来优化副作用相关的打包，这样在打包的时候，会先去确认当前 `package.json` 文件中对 sideEffects 的标识，以此来判断模块（这个项目）是否有副作用，如果这个模块没有副作用，那刚刚明明没有用到的模块就不会被打包

`package.json` 中的 sideEffects 设置为 false 的话，那就 webpack 就认为整个项目都不会有副作用，那就会把无关的模块或代码都移除掉。

上面两个地方出现了 sideEffects，webpack 配置里的 sideEffects 是开启功能，package.json 里的 sideEffects 是来标识代码有没有副作用的

使用 sideEffects 的前提是确保代码真的没有副作用，不然在 webpack 打包的时候就会误删那些有副作用的代码。比如一个模块就只是往原型链上添加方法，这种就是副作用代码，这个模块也没有导出任何成员，其实就只引入就行了，但是如果这个时候还表示项目是没有副作用代码的话，那这个刚刚的模块就会被移除，在代码中载入的 css 模块也是一样的，也是属于副作用模块

sideEffects 也可以设置成有副作用的文件的路径数组，这样在打包的时候那些文件就不会被移除

### Code Splitting

代码分包 / 代码分割

通过 webpack 实现前端项目整体模块化的优势虽然很明显，同样也有弊端，那就是所有的代码最终都被打包到一起，bundle 体积就会过大，并不是每个模块在启动时都是必要的，那又全部打包到一起，那需要任何一个模块都要先加载整体加载完成才能使用，应用又是运行在浏览器端，这就会浪费很多的流量和带宽，更合理的处理方式应该是把打包的结果合理地的分到多个 bundle 中，根据应用的运行需要按需加载，大大提高应用的响应速度和运行效率

一开始就说 webpack 是将散落的模块打包到一起，现在又要分离开，是不是自相矛盾呢？

其实不是矛盾，只是物极必反，资源太大了不行，太碎了更不行，项目中模块划分的颗粒度一般都非常细，很多时候一个模块只是提供了一个小小的工具函数，并不能形成一个完整的功能单元，如果不把散落的模块合并到一起，那就会变成即使运行一个小小的功能也会加载非常多的模块，现在主流的 HTTP1.1 就有很多缺陷，比如同于并行请求限制，每次请求都会有一定的延迟，请求的 Header 浪费带宽流量，综上所述，模块打包是必要的

不过再应用越来越大过后，要开始慢慢开始学会变通，webpack 支持一种分包的功能，也叫代码分割，它依照一定的规则打包到不同的 bundle

目前主要的分包方式有两种

- 多入口分包
  - 根据业务去配置多个打包入口，多个 bundle
- 动态导入
  - ESM

#### 多入口打包

多入口打包一般适用于传统的多页应用程序，最常见的就是一个页面对应一个打包入口，公共部分单独提取

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'none',
  entry: {
    index: './src/index.js',
    album: './src/album.js'
  },
  output: {
    filename: '[name].bundle.js' // [name] 就是 placeholder，对应 entry 的 key
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/index.html',
      filename: 'index.html',
      chunks: ['index'] // 配置了就不再引入所有的 bundle，而是引入配置的 bundle
    }),
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/album.html',
      filename: 'album.html',
      chunks: ['album']
    })
  ]
}
```

#### 提取公共模块 split chunk

不同的入口中肯定会有公共模块，如果相同的模块同时被打包到不同的 bundle 中的话，这应该是需要优化的

公共模块需要单独提取

```js
module.exports = {
  optimization: {
    splitChunks: {
      // 自动提取所有公共模块到单独 bundle
      chunks: 'all'
    }
  },
}
```

上面的配置就行了

### 动态导入

Dynamic Imports，按需加载是开发浏览器应用过程中非常常见的需求，一般说的按需加载说的是加载数据，这里说的按需加载指的是需要用到某个模块时，再加载这个模块

webpack 支持动态导入的方式去来实现按需加载，动态导入的模块会被自动分包，相比多入口的方式，动态导入更为灵活，因为可以通过代码的逻辑来控制是否需要加载某个模块，或者什么时候加载模块。而分包的目的就是为了实现按需加载，从而去提高模块的响应速度

动态导入用的就是 ESM 的动态导入，通过 `import` 函数导入指定的路径，函数返回的就是 promise，在 promise then 方法中就可以拿到模块对象

动态导入不需要任何配置，只要用 ESM 的动态导入就行了

#### 魔法注释

默认的动态导入生成的 bundle 名就是一个数字，其实没什么问题

如果你就是要命名 bundle 的话，就可以用 webpack 所特有的 Magic Comments 去实现

`import(/* webpackChunkName: 'posts' */'./posts/posts')` 这样就打包出特定名字的包了，如果 `webpackChunkName` 相同的话，就会打包到一起

### MiniCssExtractPlugin

从打包结果中提取 css 到单个文件，这样就可以实现 css 文件的按需加载

`yarn add mini-css-extract-plugin --dev`

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
module.exports = {
  mode: 'none',
  // ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // 'style-loader', // 将样式通过 style 标签注入
          MiniCssExtractPlugin.loader,
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    // ...
    new MiniCssExtractPlugin()
  ]
}
```

打包的 css 文件太小的话就不需要这个分包了

### OptimizeCssAssetsWebpackPlugin

压缩输出的 css 文件

在 webpack production 模式下，webpack 默认只会压缩 js 代码，其它文件都是要通过特定的 plugin 去处理的，对 css 的压缩用 optimize-css-assets-webpack-plugin

`yarn add optimize-css-assets-webpack-plugin --dev`

```js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const TerserWebpackPlugin = require('terser-webpack-plugin') // 默认的 JS 压缩

module.exports = {
  mode: 'none',
  // ...
  optimization: {
    minimizer: [ // 如果没有开启压缩的话，就不会用到这里配置的插件
      // 这个配置也有个缺点，就是会覆盖默认的压缩行为，导致原来的 JS 需要再去指定用什么压缩
      new TerserWebpackPlugin(),
      new OptimizeCssAssetsWebpackPlugin() // 如果配置到 plugins 中的话就是每次都要压缩 css 文件了
    ]
  },
  // ...
}
```

### 输出文件名 Hash

一般在部署前端资源文件的时候都会开启服务器的静态资源缓存，后续用户的浏览器就能缓存住应用中的静态资源，不需要再去请求缓存下来的资源，这样应用的响应速度就会得到明显的提升，不过开启静态资源缓存也可能有个小问题

如果在缓存策略中失效时间过短的话，效果就不是特别明显，如果时间过长的话，一旦应用发生了更新，重新部署过后，又没有办法更新到客户端，所以在生产模式下，建议文件名添加 Hash 值，那资源一旦改变，文件名也会跟着改变，那对于客户端而言，全新的文件名就会是全新的请求，那就没有缓存的问题，这样就可以服务器端缓存策略中的时间设置的非常长，也不用担心文件更新过后的问题

webpack 中的 filename 属性和绝大多数插件中的 filename 属性都支持占位符的方式为文件名设置 hash，它们支持三种 hash，效果各不相同

`'[name]-[hash].bundle.js'` 这里的 hash 是项目级的，项目中有任何变化，这个 hash 都会发生变化

`[name]-[chunkhash].bundle.js` chunk 级的，只要是同一路的打包，chunkhash 都是相同的

`[name]-[contenthash].bundle.js` 文件级的，根据输出文件的内容生成的 hash，不同的文件就有不同的 hash

如果默认的 20 位的 hash 太长的话，可以指定长度的 hash，比如指定 8 位的 hash 就是 `[contenthash:8]`

如果是控制缓存的话，contenthash 就是最合适的

## Rollup

rollup 同样也是一款 ES Module 的打包器，也可以将项目中细小的模块打包成整块的代码，从而是这些划分的模块可以更好地运行在浏览器环境或者 node.js 中，作用上来看 Rollup 与 Webpack 非常类似，相比于 Webpack，Rollup 要小巧的多，因为 Webpack 在配合一些插件的使用下，几乎可以完成开发过程中前端工程化的绝大多数工作，而 Rollup 仅仅是一款 ESM 打包器，并没有其它额外的功能，比如 Webpack 中有对开发者十分好的 HMR，Rollup 中就没法完全支持，Rollup 工具的诞生并不是要与 Webpack 去全面竞争，它的初衷是提供一个充分利用 ESM 各项特性的高效打包器，构建出结构比较扁平，性能比较出众的类库

### 快速上手

`yarn add rollup --dev`，装完后就会在项目下的 node_modules 的 .bin 目录下提供 rollup 的 cli 程序，我们可以是用这个 cli 程序去是用 rollup 打包

`yarn rollup ./src/index.js --format iife --file dist/bundle.js` 以 ./src/index.js 为入口和自调用函数的方式去打包，并将生成的结果生成到 dist/bundle.js 中

查看 Rollup 的打包结果会发现它十分简洁，基本上就手写的源代码是一样的，相比于 webpack 中大量的引导代码和一堆模块函数，rollup 的打包结果几乎没有多余的代码，它就是把打包过程当中各个的依赖顺序拼接到一起，且只会保留用到的部分，对未引用的部分都没有输出，因为 rollup 默认会开启 tree shaking 去优化输出的结果，tree shaking 的概念最早也是在 rollup 中提出的

### 配置文件

Rollup 也支持以配置文件的方式去配置打包过程中的各项参数

在项目根目录下新建 `rollup.config.js` 文件，这个文件也是运行在 node 环境中，不过 rollup 自身会处理这个文件，所以这个文件也可以以 ESM 的方式编写

文件需要导出一个配置对象

```js
export default {
  input: 'src/index.js',
  output: {
    file: 'dist/bundle.js',
    format: 'iife'
  }
}
```

如果要使用配置文件还是要加上参数，因为默认是不会使用配置文件的，`yarn rollup --config`，也可以指定不同文件的配置名称，比如 `yarn rollup --config rollup.prod.js`

### 插件

Rollup 自身就只是 ESM 的打包，如果我们的项目有更高级的需求，比如加载其他类型的资源文件，或者在代码中导入 CommonJS 模块，或者编译 ECMAScript 新特性，这写额外的需求，Rollup 同样支持使用插件的方式去拓展，而且插件是 Rollup 唯一的拓展方式，不像 webpack 中划分了 loader、plugins、minimizer 这三种扩展方式

```js
import json from 'rollup-plugin-json'
// 项目中引入的 json 就会被打包进来了
export default {
  input: 'src/index.js',
  output: {
    file: 'dist/bundle.js',
    format: 'iife'
  },
  plugins: [
    json()
  ]
}
```

### 加载 npm 模块

rollup 默认只能以文件路径的方式去加载本地的文件模块，对于 node_modules 中第三方的模块并不能像 webpack 一样直接通过模块的名称去导入对应的模块，为了抹平这样的差异，Rollup 官方给出了 rollup-plugin-node-resolve 这样的插件，使用这个插件就可以直接在代码中去使用第三方模块的名称去引入对应的模块了

注意，引入的第三方的模块得是 ESM 导出的，不然还是不能正确导入，如果是用的是普通版本的第三方模块还要做额外的处理

### 加载 CommonJS 模块

目前还是有很多 npm 模块使用 CommonJS 的方式去导出成员，为了兼容这些模块，官方又给出了一个插件叫 rollup-plugin-commonjs

```js
import json from 'rollup-plugin-json'
import resolve from 'rollup-plugin-node-resolve'
import commonjs from 'rollup-plugin-commonjs'

export default {
  input: 'src/index.js',
  output: {
    file: 'dist/bundle.js',
    format: 'iife'
  },
  plugins: [
    json(),
    resolve(),
    commonjs()
  ]
}
```

### Code Splitting

在最新的 Rollup 中已经开始支持代码拆分了，同样可以使用符合 ESM 标准的 Dynamic Imports 去实现按需加载，Rollup 也会自动地去处理代码的拆分，不过如果要使用代码拆分，导出的形式就得是 AMD 或者 CommonJS，且使用 output.dir 而不是 output.file

```js
export default {
  input: 'src/index.js',
  output: {
    // file: 'dist/bundle.js',
    // format: 'iife'
    dir: 'dist',
    format: 'amd'
  }
}
```

### 多入口打包

Rollup 支持多入口打包，且对于不同入口的公共部分也会自动提取到单独的文件中作为独立的 bundle

使用多入口打包要把 input 属性由字符串变成路径字符串数组，也可以以对象的形式去配置，因为多入口打包也是代码拆分，也是不能用 iife 的形式输出的

还有要注意的是，对应 AMD 的输出形式的代码文件，并不能直接去引用到页面上，而必须通过实现了 AMD 标准的库去加载，一般就是用 require.js 去加载

```html
<!-- AMD 标准格式的输出 bundle 不能直接引用 -->
<!-- <script src="foo.js"></script> -->
<!-- 需要 Require.js 这样的库 -->
<script src="https://unpkg.com/requirejs@2.3.6/require.js" data-main="foo.js"></script>
<!-- data-main 是指定 require.js 加载的入口文件 -->
```

## 选用原则

Rollup

优点

- 输出结果更加扁平，效率更高
- 自动移除未引用的代码，tree shaking
- 打包结果依然完全可读

缺点

- 加载非 ESM 的第三方模块比较复杂
- 模块最终都被打包到一个函数中，无法实现 HMR
- 浏览器环境中，代码拆分功能依赖 AMD 库

如果我们正在开发应用程序，肯定存在需要引入大量第三方模块的需求，同时我们也需要 HMR 这样的功能去提升我们的开发体验，而且应用一旦大了过后，还涉及到要去分包，Rollup 对这些需求的满足上都有些欠缺

如果我们正在开发一个框架或者类库，这些优点就特别有必要，这些缺点基本可以忽略，在开发类库的时候很少去依赖一些第三方的模块，大多数指明框架或者库都在使用 Rollup 作为模块打包器，而并非是 Webpack

开源社区中大多数人希望二者并存，共同发展，并且能够相互支持和借鉴，就是希望更专业的工具去做更专业的事情，总结就是，Webpack 大而全， Rollup 小而美

应用开发使用 Webpack，库或框架开发使用 Rollup，这并不是绝对的，只是经验法则，因为 Rollup 也能去构建绝大多数的应用程序，而 Webpack 也同样能去构建类库或者框架，只不过相对来说，术业有专攻，随着近几年的发展，Rollup 的优势被渐渐抹平了，例如 Rollup 中的扁平化输出，Webpack 中就可以使用 webpack.optimize.ModuleConcatenationPlugin 去完成，也可以实现类似的输出

## Parcel

零配置的前端应用打包器

提供了近乎傻瓜式的使用体验，只要了解一些简单的构建命令就可以去构建前端应用程序了

`yarn add parcel-bundler --dev` 

虽然 Parcel 和 Webpack 一样都支持任意文件作为打包入口，但是 Parcel 官方还是建议使用 HTML 作为打包入口，官方给出的理由是 HTML 在浏览器中是应用运行的入口

执行 `yarn parcel src/index.html` 命令后，不但会打包，还会打开浏览器载入页面并开启类似 webpack-dev-server 的开发服务器自动刷新页面方便我们的开发

HMR 的方法跟 Webpack 有点像

```js
if (module.hot) {
  module.hot.accept(() => { // Webpack 支持两个参数，对特定模块的重新导入逻辑，Parcel 是全部自动重新导入
    console.log('hmr')
  })
}
```

而且 Parcel 能让我们在开发的时候自动安装依赖，比如项目本来是没有 jQuery 的，在你写入 `import $ from 'jquery'` 并使用 `$` 保存代码后，Parcel 就会自动去安装依赖

Parcel 也支持加载其他类型的资源模块，而且也是零配置的

Parcel 也支持动态导入，如果写了动态导入的代码，也能自动拆分文件

Parcel 让你专注于开发业务逻辑

`yarn parcel build src/index.html` 就可以以生产模式打包了，相同体量的项目，Parcel 打包会比 Webpack 快很多，因为在 Parcel 的内部是使用的多进程同时去工作，充分发挥了多核 CPU 的性能，在 Webpack 也能是用一个叫 happypack 的插件去实现这一点

Parcel 是 2017 发布的，因为当时 Webpack 使用上过于繁琐，而且官方的文档也不是很清晰明了，所以 Parcel 一推出就迅速被推上了风口浪尖，起核心特点就是真正做到了完全零配置，对项目没有任何的侵入，且开发过程中有自动安装依赖的体验，让我们可以更专注于编码，还有 Parcel 默认提供的构建速度就更快

但其实再绝大数的项目打包还是会使用 Webpack，原因可能是

- Webpack 有更好的生态，出现问题更容易去解决
- 随着这两年的发展，Webpack 也越来越好用，开发者也越来越熟悉

学习 Parcel 就是为了保持对新鲜技术和工具的敏感度，从而更好的把握技术的走向



学习一个新事物不是说学会它的所有用法就能提高，这些东西照着文档基本上谁都可以，而很多时候新事物的思想才是突破点，搞清楚新事物为什么这样设计才是至关重要的

