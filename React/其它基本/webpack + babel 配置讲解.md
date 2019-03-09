# webpack 与 babel 配置详解

本次扩展，我们会根据我们的扩展项目：“React 博客项目开发环境搭建”所需要的 webpack 配置来做解读。

首先我们来看一下我们博客项目中的 webpack 配置：

```
var path = require('path');
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    mode: 'development',
    entry: {
        main: path.join(__dirname, './src/pages/Main/index'),
        article: path.join(__dirname, './src/pages/Article/index')
    },
    output: {
        path: path.join(__dirname, './dist'),
        filename: '[name].min.js'
    },
    module: {
        rules: [
            {
                test: /\.jsx?$/,
                exclude: /node_modules/,
                loader: 'babel-loader'
            },
            {
                test: /\.less$/,
                loader: 'style-loader!css-loader!less-loader'
            },
            {
                test: /\.(png|jpg|gif)$/,
                use: [
                    {
                        loader: 'file-loader',
                        options: {}
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            filename: 'index.html',
            template: path.join(__dirname, './index.html'),
            chunks: ['main']
        }),
        new HtmlWebpackPlugin({
            filename: 'article.html',
            template: path.join(__dirname, './index.html'),
            chunks: ['article']
        })
    ],
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    externals: {
        "react": 'React',
        'react-dom': 'ReactDOM'
    }
}
```

接下来我们会对各个部分进行解释。

## entry

即入口，入口起点(entry point)指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

配置 entry 的方式比较多，这里我们采用的是对象写法，虽然这种方式会比较繁琐，但是这是应用程序定义入口的最可扩展的方式，我们可以在之后的 output 配置和一些自定义插件中使用我们配置的键名等信息。

另外，更多配置方式可以直接参考[这里](https://www.webpackjs.com/concepts/entry-points/)。

## output

output 属性告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件，默认值为 ./dist。基本上，整个应用程序结构，都会被编译到你指定的输出路径的文件夹中。

根据我们上文的配置，我们最终会在指定的路径下，即 `path.join(__dirname, './dist')`，生成 `main.min.js` 和 `article.min.js`。

## module

webpack 通过 loader 可以支持各种语言和预处理器编写模块，我们可以通过在模块中配置 loader 的方式，来支持我们在 JavaScript 文件中引入非 JavaScript 文件（比如引入图片资源或者样式资源），以及支持我们使用非标准 JavaScript 文件（比如使用 jsx 文件或 vue 文件）。

对于我们的项目来说，我们使用 react 的 jsx 语法，并且使用 less 来编写样式文件，此外，我们会在项目中引入一些图片资源，所以我们配置了以上三个插件。

## Babel 配置

这里对于 `'babel-loader'` 的配置，我们需要进一步拓展说明，实际上，一方面我们项目中使用了 react，所以需要配置使用对应的 react 扩展即 `babel-preset-react`，另外一方面，由于我们会在项目中使用一些目前浏览器支持不太好的 JavaScript 语法（这方面的内容，可以参考 ECMAScript 标准），因此我们需要使用 babel 配置转义，项目中使用了 `babel-preset-env`。

项目配置的方式，是在项目文件夹下新建 `.babelrc` 文件，并且写入：

```
{
    "presets": ["env", "react"]
}
```

另外，我们需要保证相关的扩展已经安装：

```
npm install babel-preset-env babel-preset-react --save-dev
```

## plugins

插件（plugins）是 webpack 的一个核心功能，我们可以通过配置插件流水线的方式来实现各种高级功能和高级配置，webpack 本身也构建于插件系统之上。

但是对于我们的博客项目来说，我们项目较为简单，因此没有配置复杂的插件，只使用了一个`html-webpack-plugin`，这个插件的作用是，在 webpack 构建的过程中，在对应目录下生成 html 文件，并且可以正确地引入 webpack 生成的文件。

## externals

外部扩展（externals）配置选项提供了「从输出的 bundle 中排除依赖」的方法。也就是说，在对应输出的文件中，不包含对应库文件的内容，但是我们仍然可以在项目中正常使用。

对于我们的博客项目，我们通过 cdn 的方式引入 React 和 React-dom，因此我们将其配置在了 externals 中

```
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
```

## resolve

resolve 用于配制 webpack 的解析规则，其中我们项目中配置的 resolve.extensions ，作用是按照给定的顺序解析扩展名，从而能够使用户在引入模块的时候不需要带扩展名，仍然能够正确的解析。

## 总结

对于 webpack 的配置规则，实际上非常复杂，也很难通过一篇文章讲解清楚，本文仅仅分析了我们目前学习中遇到的内容，如果想学习更多的内容，可以去[webpack中文文档官方网站](https://webpack.docschina.org/)来学习更多内容。