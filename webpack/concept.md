**一下内容是 webpack4，有可能不一样了**

webpack 会生成很多模块依赖的代码，所以不够纯粹，不太适合开发库，不是不能开发

## entry

## output

## mode

## module

webpack 只认识 js，所有有其他类型的文件引入就需要 loader 来处理转换了

module 是一个对象，里面有一个 rules 属性，rules 是一个数组，用来检测不同后缀的文件，relus 里面每一项都是一个对象

### loader

loader 用于对模块的源代码进行转换。loader 可以使你在 `import` 或"加载"模块时预处理文件。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 `import` CSS文件！

```js
module: {
	rules: [
		{
			test: /\.(png|jpe?g|gif)$/,
			use: [
				{ // 当这个 loader 需要配置的时候，就要由字符串变成对象
					loader: "file-loader",
					options: {
						name: "[name]_[hash:8].[ext]" // 生成名字加8位hash再加拓展的文件件名
					}
				}
			]
		}
	]
}
```

**loader 是有执行顺序的，顺序是自后往前**



## plugins

有一个选项是可以定义自己的 plugins 的目录，然后就不用引入完整路径

跟 loader 是完全不一样的概念，如果是不认识的模块，那就是在 module 通过 loader 处理，而 plugins 特别强大，可以作用于 webpack 构建的整个生命周期。

webpack 有着丰富的插件接口(rich plugin interface)。webpack 自身的多数功能都使用这个插件接口。这个插件接口使 webpack 变得**极其灵活**。

plugins 也是一个数组

plugin 都是一个类，里面包含一个 apply 函数，接受一个参数 compiler，由事件驱动，发布订阅

这个参数 compiler 上有很多钩子，让我们可以访问到 webpack 打包时候的各种时刻 https://www.webpackjs.com/api/compiler-hooks/

```js
// 这些都是写在 apply 函数内的
// 生命周期在 hooks 上，下面这个是同步的
compiler.hooks.someHook.tap(...s);

// 异步的一般是在某些钩子上访问 tapAsync 和 tapPromise。
compiler.hooks.emit.tapAsync("CopyrightWebpackPlugin", (compilation, cb) => {
	compilation.assets["copyright.txt"] = {
    source: function() {
      return "hello copy"; 
    },
    size: function() {
      return 20;
    }
  };
  // plugin 需要做的事情完成之后需要走回调，告诉 compilation 事件结束
  cb();
});
```

异步在某些钩子上访问 `tapAsync` 和 `tapPromise`。

## devtool

```
// 开发环境配置
devtool:"cheap-module-eval-source-map"

// 线上不推荐开启，应该设置成 none 
```

## devServer

开发服务器，devServer 是一个对象

### contentBase 

服务器路由根路径

### port

### open

服务启动的时候自动打开浏览器

### proxy

proxy 是一个对象，字段名字是接口的前缀，根据不同的前缀可以配置不同的 proxy 转发

```
proxy: {
	"/api": {
		target: "http://localhost:9000"
	}
}
```

可以自己写个简单的 node 后端服务来 mock 数据开发前端

### hot

Hot module replacement ( HMR: 热模块替换 )

### hotOnly

即使 HMR 不生效，浏览器也不自动刷新，就开启 hotOnly

webpack 内自带 HotModuleReplacementPlugin 插件

开启热更新后，对 css 支持比较好，但是 js 热更新还要进行额外的设置，是使用 `module.accept(dep, callback)` 方式去写依赖和回调（感觉这也太难看了）

HMR 支持戳 style-loader css 处理方式，不支持抽离成独立文件的方式

## 提升构建速度

安装最新 node

写代码在引入模块的时候要写上缀，名字写全

利用第三方工具实现缓存构建

将那些公用的代码库，比如工具库，react 库，都是没有打包的必要的，抽出来

**主要参考** https://www.webpackjs.com/guides/build-performance/

