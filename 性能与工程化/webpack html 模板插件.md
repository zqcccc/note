# Webpack html 模板

前面介绍的例子中我们已经将 js，css 打包并压缩到 `./dist` 目录下了，但是项目的 `index.html` 还是在根目录下，而且页面引用打包后的 js 的路径也是我们手动加上去的。而实际项目中我们希望把 html 文件也加入构建中，并且自动将打包后的 js 加到 html 中，甚至可以使用不同的模板自动生成 html 文件，在 Webpack 里面需要 `html-webpack-plugin` 插件来实现。

[html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) 是 Webpack 的插件，可以用于自动生成 html 文件或者复制 html 文件并且将打包后的 js 加入到 html 中。

在前面练习的项目基础上，我们要用这个插件将项目中的 `index.html` 复制到 `dist` 目录中，下面是具体操作步骤。

1. 首先将 `index.html` 放到 `src` 目录，并且删掉 `script` 标签。
2. 命令行输入 `npm install html-webpack-plugin --save-dev` 安装插件
3. 修改构建文件，在 `plugins` 数组中加入该插件的任务，修改后的 `webpack.config.js` 内容如下：

```js
var path = require('path');
var webpack = require('webpack')
var UglifyJSPlugin = webpack.optimize.UglifyJsPlugin;
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, './dist')
    },
    module: {
     rules: [{
      test: /\.css$/,
      use: ['style-loader', 'css-loader']
     }]
    },
    plugins: [
      new UglifyJSPlugin(),
      // 加入 html 模板任务
      new HtmlWebpackPlugin({
          // 模板文件
          template: 'src/index.html',
          // 打包后文件名称，会自动放到 output 指定的 dist 目录
          filename: 'index.html'
      })
    ]
}
```

经过上面修改，现在我们项目的 index.html 也加入构建了。

到目前为止，我们所有的 html, css, js 文件源代码都放到 `src` 目录， 而打包后的文件都放到 `dist` 目录。这样，我们开发的时候，就只需要关注 `src` 目录的代码，而发布的时候，只需要将 `dist` 目录下的文件放到服务器就可以了，这也是我们现在前端工程化通常的做法。

另外，将所有文件加入构建还有个好处就是当我们用到下一节说到的开启本地调试服务器时，构建可以检测到所有源文件的变化，并将所有打包后的文件放到本地开启的服务器中，这样我们修改的代码都可以在本地服务器中看到效果。