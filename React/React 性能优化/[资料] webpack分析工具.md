# webpack分析工具: webpack-bundle-analyzer

webpack-bundle-analyzer 作为一个 webpack 插件，可以帮助我们分析出项目中各个模块文件的大小，我们可以根据其进行对应的优化。

我们先来看一张官方的效果图：

![图片](http://p.qpic.cn/qqconadmin/0/9341b5c721cb4eb4948c497e40dc7804/0)

## 安装

```
npm install --save-dev webpack-bundle-analyzer
```

## webpack 配置

```
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
}
```

基础的配置十分简单！这个时候我们可以直接执行构建了，构建完成后会自动打开一个可视化的窗口，和文章开头的效果图一样。
更多详细的配置参考 [官方文档](https://www.npmjs.com/package/webpack-bundle-analyzer)。