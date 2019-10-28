安装：

`npm i jest -D`

node 8 以上的版本，项目目录下运行：

`npx jest`

也可以修改 `package.json` 里的 `script` 里的脚本

一般是 `test` ，改成 `"test": "jest"` 

然后 `npm run test` 或者 `npm t` 就可以执行测试了

## xxx.test.js 

test 就是 test case

expect 就是 assert

`describe('test', () => { /* 这里放同一函数很多的 test() */ })` 其他测试框架里叫 test suite 在 jest 里我们可以叫它 test group 即测试分组，因为在 jest 里 test suite 测试套件是一个文件

虽然是零配置，但是测试文件要以 `**test.js` 结尾

- [英文文档](https://facebook.github.io/jest/docs/en/getting-started.html)
- [中文文档](https://jestjs.io/zh-Hans/)

## 配置

零配置不代表不可以配置

配置位置

- package.json
- jest.config.js
- 命令行，独有的 option（不推荐），几乎囊括了所有的常规参数
  - `--watch` 就是监听代码自动执行测试，这个要求你的项目必须用了 **git**
  - `--coverage` 显示代码覆盖率，后面会说这个代码覆盖率

配置项

- testMatch 是一种 glob 配置匹配文件
- testRegex 正则匹配文件，和 testMatch 互斥，
- testEnvironment
- rootDir 默认值：package.json 所在目录，没有 package.json 就是 pwd 即当前目录
- moduleFileExtensions 默认值 `['js', 'json', 'jsx', 'node']`

可以参考文档：

- [命令行](https://facebook.github.io/jest/docs/en/cli.html)
- [配置](https://facebook.github.io/jest/docs/en/configuration.html)

最后，命令行的值，优先级较高，比如你配置文件和命令行同时都有 `testMatch` ，那最终会取命令行里的值。

## Jest Matchers

相等

- **toBe** ===
- **toEqual** 对象和数组
- **toBeNull**
- **toBeUndefined**

包含：对象、数组、字符串

- **toHaveProperty**
- **toContain**
- **toMatch**

逻辑

- **toBeTruthy** 不一定是 true ，一个对象也可以
- **toBeFalsy**
- **toBeGreaterThan**
- **toBeLessThan**

**not** 取反

## DOM 测试

因为 **testEnvironment** 里的默认值有 `jsdom` ，jsdom 就是一个 js 实现了 WHATWG DOM and HTML 标准给 nodejs 用的 npm 包，是给测试用的浏览器的子集，但是不能测试兼容性，如果要考虑兼容性，还是要去各个浏览器里去测试

**Chrome headless** 是真实的 **Chrome** ，就差 UI 界面了，自然而然它的效果比 **JSDOM** 之类的模拟库要好的多，可以调用 chrome headless 去渲染页面和 dom 树，然后操作

phantomjs 是 Chrome headless 之父

## 测试异步流程

test() 第二个参数是个回调，它的参数是 done() 也是回调，异步执行结束后调用这个回调就是测试异步的方法

## Mock

擦除函数的实际实现来测试代码之间的链接

捕获对函数的调用

manual_mocks 用来 mock 依赖的模块，放置在相应 `_mocks_` 目录下

使用 mock function ，可以查看函数的调用次数，以及入参情况

## 测试 React 组件

`enzyme` `jest` `react-test-renderer` 这三个包一定要有

enzyme 是专门测试 react 的

shallow 浅渲染，只渲染父组件

普通的 `css` 模块引入会报错，这个属于 webpack 的内容，如果引入的其他资源也要去 **mock**，在测试中也不会看到样式，所以要在 `jest` 的配置中的 `moduleNameMapper` 设置一个 `mock`，一般是这样 `"\\.css$": "<rootDir>/__mocks__/styleMock.js"` ，而这个 `styleMock` 导出的就是一个空对象

[Airbnb 官方出品 enzyme 仓库](https://github.com/airbnb/enzyme)

[enzyme 官方文档](https://airbnb.io/enzyme/)

## 代码覆盖率

Code Coverage 一种度量，描述代码被测试的比例和程度

- line coverage (一行可能有很多 statement)
- function coverage
- branch coverage (if ... else ...)
- statement coverage

框架：**Istanbul** （jest 内置）

`npx jest --coverage` 就可以查看 code coverage 了，还会一并生成一个 `coverage` 的文件夹，里面有代码测试执行的详细情况

jest 代码覆盖率相关的配置项 —— **coverageThreshold**

threshold，阈值的意思。给大家说过，代码覆盖率不一定非要 100% ，根据业务、团队等等情况，可以自定义自己的阈值，在 jest 中，就是通过 **coverageThreshold** 来配置的，来个例子。

```json
{
  ...
  "jest": {
    "coverageThreshold": {
      "global": { // 全局配置
        "branches": 95,  // 95 就是覆盖率达到 95% 咯
        "functions": 95,
        "lines": 95,
        "statements": 95
      },
      "./src/components/**/*.js": { // 给特定的文件独特的配置，会覆盖上面的全局配置
        "branches": 40,
        "statements": 40
      },
      "./src/api/very-important-module.js": { // 同理
        "branches": 100,
        "functions": 100,
        "lines": 100,
        "statements": 100
      }
    }
  }
}
```

当你配置好后，执行的结果有些不同，如下图是添加 **coverageThreshold** 之前的执行结果：

![之前](http://coding.imweb.io/img/p8/coverage-threshold-before.png)

添加之后，底部多了一行：

![之后](http://coding.imweb.io/img/p8/coverage-threshold-after.png)

## 因为测试，所以专业

TDD 中最精华的应该是**重构**，好的代码一定是很容易被测试的

特点：

- 模块
- 函数

好的测试用例：

- 尽量穷尽
- 边界
- 简单
- 独立
- 重复执行（mac，win 都可以）