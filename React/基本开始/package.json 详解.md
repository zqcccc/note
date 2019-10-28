# package.json 详解

我们在视频中提及，package.json 可以通过 `npm init` 来交互式地创建，而事实上，package.json 中可写的内容非常多，接下来我们在本扩展中讲解更多的字段内容。

## name 字段 和 version 字段

name 和 version 字段是 package.json 文件中最重要的字段。这是必须的字段，如果你的 npm 包没有指定这两个字段，将无法被安装。name 和 version 字段被假定组合成一个唯一的标识符。包内容的更改和包版本的更改是同步的。

因此，一般我们认为一个最简单的 package.json 文件如下所示：

```
{
  "name" : "xxx",
  "version" : "0.0.1",
}
```

另外，对于 name 字段的命名有如下规则限制：

1. name 的长度必须小于等于214个字符。
2. name 不能以"."(点)或者"_"(下划线)开头。
3. name 中不能包含大写字母。
4. name 最终将被用作URL的一部分、命令行的参数和文件夹名。因此，name 不能含有非URL安全的字符。

## scripts 字段

scripts 指定了运行脚本命令的 npm 命令行缩写，比如 start 指定了运行 `npm run start` 时，所要执行的命令。

下面的设置指定了 `npm run dev、npm run dist、npm run pub、npm run start、npm run test` 时，所要执行的命令。

```
"scripts": {
   "dev": "anyproxy --port 8080 --rule proxy.js & npm run start",
   "dist": "cross-env NODE_ENV=production node ./tools/script.js",
   "pub": "npm run dist",
   "start": "cross-env NODE_ENV=development node ./tools/script.js",
   "test": "node ./test.js",
}
```

我们从上面也可以看出，我们可以在一个字段命令中运行另外一个字段。

## bin 字段

bin 字段用来指定各个内部命令对应的可执行文件的位置，一般而言，对于需要全局安装的包，我们都有该字段：

```
"bin": {
  "myCommand": "./bin/index.js"
}
```

上面的代码中指定，`myCommand` 命令对应的可执行文件在同级别目录下的 `bin/index.js` 中，因此 npm 在安装该包的时候就会在 `node_modules/.bin` 的目录下建立符号链接，对于全局安装的包，由于 `node_modules/.bin` 会在运行时加入系统的 PATH 变量，因此可以直接通过命令来调用脚本。

另外，如果我们是在项目内安装包，也可以通过 `npx myCommand` 的方式来进行调用。

## main 字段

main 字段指定了加载的入口文件，`require('moduleName')` 就会加载这个文件。

对于 Node 来说，这个字段的默认值是模块根目录下面的 index.js。

## config 字段

config 字段可以添加命令行的环境变量，例如下面的 package.json 文件：

```
{
  "name" : "buluo",
  "config" : { "port" : "8080" },
  "scripts" : { "start" : "node index.js" }
}
```

在 index.js 中，我们可以直接使用该环境变量：

```
http
  .createServer(...)
  .listen(process.env.npm_package_config_port)
```

## 一些其他的描述性字段

接下来我们介绍一些描述性字段，这些描述性字段一般会在 npm 包的检索、管理等中起到一定作用。

keywords ：npm 包的关键字，keywords 是一个字符串的数组。它可以帮助人们在使用 `npm search` 时找到这个包。
homepage ：项目主页的 url。
bugs ：改项目的 issue 跟踪页面或这报告 issue 的 email 地址。这对使用这个包遇到问题的用户会有帮助，例如：

```
{ 
  "url" : "https://github.com/owner/project/issues",
  "email" : "project@hostname.com"
}
```

license ：指定该 npm 包所使用的协议。
author：包作者名字，一般使用数组或者字符串，格式如下：

```
{
    "name": "Barney Rubble",
    "email": "b@rubble.com",
    "url": "http://barnyrubble.tumblr.com/"
}
```

或者

```
"Barney Rubble <b@rubble.com> (http://barnyrubble.tumblr.com/)"
```

contributors： 贡献者列表，是一个数组，每一个单位的格式和 anthor 相同。
repository：指明你的代码被托管在何处，这对那些想要参与到这个项目中的人来说很有帮助。如果 git 仓库在 github 上，用 `npm docs` 命令将会找到你，例如：

```
{
    "type": "git",
    "url": "https://github.com/npm/npm.git"
}
```

以上便是对一部分 pacakge.json 的字段的介绍，实际上，package.json 的字段不止这些，但是一般而言，了解以上字段已经足够我们日常的学习和使用了。

更多字段可以参考[官方文档](https://docs.npmjs.com/files/package.json)