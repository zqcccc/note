# 工程化

前端工程化在前端开发中扮演着重要的角色，是团队开发中必不可少的东西。

- 通过流程规范化、标准化等提升团队协作效率。
- 通过组件化、模块化开发等提升代码质量。
- 使用构建工具、自动化工具等提升开发效率。

## 构建工具

### tsc 编译

tsc 常用参数

- `rootDir`, `outDir` -- 配置输入输出目录
- `lib`, `jsx` -- 添加基础库的能力
- `nolmplicitAny`, `strict` -- 更严格的模式
- `module`, `target`, `declaration`, `sourceMap` -- 控制输出的内容
- `watch` -- 观察模式

`tsc` 是 `TS编译器` 提供的编译命令。使用 `tsc` 命令可以将 `ts` 文件编译为 `js` 文件。

`tsc` 命令可以接收一个编译选项文件 `tsconfig.json`。该文件可以设置一系列编译规则，`TS编译器` 会根据这些编译规则生成相应的 `js` 文件。

### tsconfig.json 常用配置

https://www.typescriptlang.org/docs/handbook/tsconfig-json.html

https://www.tslang.cn/docs/handbook/tsconfig-json.html

`"compilerOptions"` 是编译选项（对象）

- `experimentalDecorators` 的含义是启用实验性的ES装饰器（布尔）
- `lib` 指定 es 版本，这是个字符串数组
- `strict` true of false
- `target` 一般是 "es5"
- `outDir` 是编译生成的文件所在的目标文件夹
- `sourceMap` boolean 值指定是否生成 source map

 `"include"` 是包含的文件（数组）

 `"exclude"` 是去除的文件（数组）

### webpack

## 测试

这里以 mocha 为例，实际上 jest 也很流行

```bash
npm i mocha @type/mocha @type/node ts-node --save-dev
```

在根目录创建一个 `test` 目录

里面创建 `xxx.test.js` 的文件，对应不同文件的测试文件

向 `npm scripts` 中添加

```json
"test": "TS_NODE_COMPILER_OPTIONS='{\"module\":\"commonjs\"}' mocha --require ts-node/register test/xxx.test.js"
```

这里的 module 要是 commonjs，但是又不想修改 `tsconfig.json` 所以就在这条 script 里添加了 `TS_NODE_COMPILER_OPTIONS`

如果 mocha 后面的参数太多了，可以在 test 目录下创建一个文件叫 mocha.opts 把参数都写到里面就行了

```mocha.opts
--require ts-node/register 
--watch
--watch-extensins ts
test/xxx.test.js
```

默认也不会 watch 我们修改的 ts 文件，所以要加上 `--watch-extensins ts` 

**总结**：使用 `mocha` 测试 `ts` 的步骤为：

- 全局安装 `typescript` 和 `ts-node`

  - `ts-node` 可以直接执行 `ts` 源文件而不需要预先编译

- 项目目录安装

  - 测试框架 `mocha`
  - 断言库 `chai`

- 编写待测试的代码 `add.ts`

- 编写测试代码 `add.test.ts`

- 执行测试

  - `mocha -r ts-node/register add.test.ts`

## 持续集成

持续集成强调开发人员提交了新代码之后，立刻进行构建、（单元）测试。根据测试结果，我们可以确定新代码和原有代码能否正确地集成在一起。

通过持续集成，我们可以更早的发现错误，解决问题。把大量的重复工作交给机器去完成，解放劳动力，从而提升团队开发效率。持续集成有效的保障了项目的高质量完成。

## 迁移代码

ts 是 js 的超集

使用 `allowJs` 和 `checkJs` 可以 tsc 在编译的时候也编译 js，ts 是很包容的，现有的项目可以直接向 ts 靠拢，新代码都用 ts 来写，后面再慢慢换就行了

js 里也可以通过添加注释的方式为代码提供类型

