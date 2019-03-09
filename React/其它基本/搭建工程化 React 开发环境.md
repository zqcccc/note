工程化 vs 非工程化

工程化，有计划指导，步步可循，可以并行合作

特点：过程可重复，自动化，适合大规模多人协作，可维护性好

- 设计图纸	npm init 生成或拷贝现成的 package.json
- 准备材料	npm install 安装依赖包
- 制作零件	开发各个代码模块
- 组装零件	配置并运行打包工具、编译工具等

非工程化，非常随意

特点：维护性差，缺乏标准流程，难以重复

- 直接上手
- 检查是否满足需求
- 修修补补直到满足需求



- webpack 包括各种 loader 和插件
  - 根目录下用 `webpack.config.js` 配置 `webpack`

```

```



- babel 包括各种 preset
  - 根目录下用 `.babelrc` 配置 `babel`

```json
{
    "presets": ["es2015", "react"]
}
```



- less / sass
- react
- react-dom