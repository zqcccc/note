# npm 简介与常用命令

## 什么是 npm

npm 的全称是 Node Package Manager，是一个随 Node 一起安装的 Node 包管理和分发工具，我们可以通过 npm 命令来实现 Node 的模块的安装和卸载。

一般情况下，npm 的使用情况有这样几种：

- 从 npm 服务器下载第三方包到本地。
- 从 npm 服务器下载并安装别人的命令行程序到本地。
- 将自己便携的 npm 包或者命令行程序上传到 npm 服务器。

更多的信息，我们可以参考[ npm 官方网站](https://www.npmjs.com/)。

实际上，Node 的包管理器除了 npm，还有 Facebook 推出的 yarn 与淘宝推出的 cnpm。

### package.json

在项目中，npm 一般需要配合 package.json 文件来使用。每个项目的根目录下面，一般都有一个 package.json 文件，定义了这个项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）。`npm install` 命令根据这个配置文件，自动下载所需的模块，也就是配置项目所需的运行和开发环境。

package.json 中的字段较多，大部分是和项目基本信息有关的字段，对于其具体解读，我们会在其他的扩展中给出，这里我们不做过多介绍，我们只关注和安装包关系密切的字段：

- dependencies 字段指定了项目运行所依赖的模块
- devDependencies 字段指定项目开发所需要的模块。

它们都指向一个对象，该对象的各个成员，分别由模块名和对应的版本要求组成，表示依赖的模块及其版本范围，下面是一个 devDependencies 字段的示例：

```
{
  "devDependencies": {
    "browserify": "~13.0.0",
    "karma-browserify": "~5.0.1"
  }
}
```

对于一个新项目，我们一般可以执行 `npm init`，来交互式地自动生成 package.json 文件。

## npm 常用命令

### npm 安装模块

npm 安装模块一般有两种方式：全局安装和项目内安装，一般对于我们需要全局使用的命令行程序等，我们采用全局安装，而对于项目中依赖的包模块，我们采用项目内安装的方式。

全局安装包的命令为：

```
npm install <Module Name> -g
```

这样安装的包一般会在本机 Node 的安装目录，对于 Linux 系统来说，这个目录默认一般是 `/usr/local/lib/Node_modules`， 而 windows 会在安装 Node 的文件夹中的 node_module 文件夹内。

全局安装的包，一般会有暴露的命令行命令，可以直接使用。

项目内安装包的方式为：

```
npm install
npm install <Module Name>
npm install <Module Name> --save
npm install <Module Name> --save-dev
```

`npm install`为依据 pacakge.json 中的信息安装模块。

我们采用项目内安装包的方式，会直接安装在项目的 `node_module` 文件夹下，如果我们增加了 `--save`，则会在项目的 package.json 的 dependencies 下写入该包的新秀以做保存。

实际上，我们还可以通过 `--save-dev` 的方式来将包信息保存在 package.json 的 devDependencies 下，从而区分开发环境和生产环境。

### npm 更新模块

更新模块的命令非常简单，如果我们想更新项目中的模块，可以使用如下命令：

```
npm update <Module Name>
npm update <Module Name>@<Version>
```

update 默认更新到最新版本，如果我们需要更新到指定版本，需要通过比如 `npm update express@4.0` 的方式增加版本号。

如果我们想更新全局模块，只需要在命令后加上 `-g` 即可。

### 卸载模块

npm 卸载项目中的模块，可以在项目目录中执行：

```
npm uninstall <Module Name>
npm uninstall <Module Name> --save
npm uninstall <Module Name> --save-dev
```

其中的第一个方式，为卸载模块但是不删除 package.json 中的记录，第二个方式，为卸载模块并删除在 package.json 的生产环境中(dependencies)的记录，第三个方式为卸载模块并删除在 package.json 开发环境中的记录。

另外，我们可以通过 `npm remove -g <Module Name>` 的方式移除全局模块。

以上就是一些 npm 的简介与常用命令。