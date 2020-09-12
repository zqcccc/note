# Parcel

[Parcel](https://parceljs.org/) 官方对自己的介绍是「极速零配置 Web 应用打包工具」，从这里我们就能看出 `Parcel` 的主要目标就是尽量降低我们前期的配置成本，更加聚焦于业务的开发。同时 `Parcel` 对于 `TypeScript` 有着天然的支持。

## Parcel 简单使用

### 安装

```bash
yarn global add parcel-bundler
```

or

```bash
npm install -g parcel-bundler
```

### 命令行使用

假设我们有如下文件：

```html
// index.html
<html>
  <body>
    <script src="./index.js"></script>
  </body>
</html>
// index.js
console.log("hello world");
parcel index.html
```

就这样一行简单的命令，我们就拥有了热更新能力的 web 服务，这时候打开 http://localhost:1234/ 就可以看到效果。我们也可以通过 `-p <port number>` 来指定端口。如果我们项目有自己的服务器，也可以在 watch 模式下运行 Parcel 。当文件改变它仍然会自动重新构建并支持热替换，但是不会启动 web 服务。

```bash
parcel watch index.html
```

关于 `Parcel` 的详细介绍，可以参看官方文档：https://parceljs.org/ 和官方仓库：https://github.com/parcel-bundler/parcel