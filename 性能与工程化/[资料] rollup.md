# Rollup

[Rollup](https://rollupjs.org/guide/en) 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码，使用标准化的 `ES Module` 代替以前的特殊解决方案，如 `CommonJS` 和 `AMD`。`ES Module` 可以让我们自由、无缝地组合库中最有用的各个功能。`Rollup` 也可以优化 `ES Module`，以便在现代浏览器中加快原生加载速度，或者输出传统模块格式，从而实现`ES Module`工作流程。
与 `Webpack` 和 `Precel` 的区别在于侧重点的不同，`Rollup` 更适用于构建 Library, 而 `Webpack`, `Precel` 更加适合开发 Application。

## Rollup 简单使用

### 安装

```bash
npm install --global rollup
```

### 命令行使用

```bash
＃编译成包含自执行函数的 <script>
rollup main.js --format iife --name "myBundle" --file bundle.js

＃编译成模块CommonJS的
rollup main.js --format cjs --file bundle.js

＃ 编译成 UMD 格式，这里需要一个包名称
rollup main.js --format umd --name "myBundle" --file bundle.js
```

### 配置文件使用

```bash
rollup --config rollup.config.js
// rollup.config.js
module.exports = {
  input: "src/main.js",
  output: {
    file: "bundle.js",
    format: "cjs"
  }
};
```

关于 `Rollup` 的详细使用，可以参见官方文档： https://rollupjs.org/guide/en，仓库地址：https://github.com/rollup/rollup