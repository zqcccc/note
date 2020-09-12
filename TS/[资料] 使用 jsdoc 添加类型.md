# 使用 JSDoc 添加类型

在 TypeScript 2.3+ 的版本中，支持通过开启 `--checkJS` 选项来支持 `.js` 文件的类型检查和错误报告。

我们可以通过添加 `// @ts-nocheck` 注释来跳过检查某些文件; 相反，也可以通过添加 `// @ts-check` 注释来检查几个文件而无需进行设置 `--checkJs`。

以下是 `.js` 和 `.ts` 文件中，类型检查值得注意的一些区别。

## JSDoc 类型被用于类型信息

我们可以为 `.js` 文件添加符合 JSDoc 规范的注释来为其申明类型，例如：

```js
/** @type {number} */
var x;

x = 0; // OK
x = false; // Error: boolean is not assignable to number```
```

## 在 TypeScript 中受支持的 JSDoc 语法

- @type
- @param（@arg 或@argument）
- @returns（或@return）
- @typedef
- @callback
- @template
- @class（或@constructor）
- @this
- @extends（或@augments）
- @enum

详细介绍可以参考：https://github.com/Microsoft/TypeScript/wiki/JSDoc-support-in-JavaScript