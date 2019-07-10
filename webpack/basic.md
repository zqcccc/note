老师居然是 webpack 的核心成员 Sean Larkin，再次感叹 frontendmasters 真的好

可以将 webpack 安装到 dev 依赖下然后用 `node_modules\.bin\webpack -v` (windows 使用 \)

css-loader 用于加载 .css 文件，并且转换成 commonjs 对象

style-loader 将样式通过 `<style>` 标签插入到 head 中

url-loader 和 file-loader 差不多，都是引入文件的，不过 url-loader 可以将小的文件转化成 base64 来引入

webpack 开启监听模式，有两种方式（需要刷新浏览器）：

1. 启动 webpack 命令时， 带上 `--watcb` 参数
2. 在配置 webapck.config.js 中设置 `watch: true`

webpack-dev-server 热更新，不需要刷新浏览器，不输出文件，而是放在内存中，使用 `HotModuleReplacementPlugin` 插件