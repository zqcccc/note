# React性能分析工具

React 16.5 加入了 Profiler 功能，我们可以通过该功能，在 DevTools 插件中记录每次渲染相关的信息，进而发现潜在的性能问题，帮助我们开发出性能更佳的应用。
需要注意，这个功能在 DEV 模式下是默认开启的。如果想要在正式环境中开启，可以使用 react-dom/profiling，点击 [这里](https://gist.github.com/bvaughn/25e6233aeb1b4f0cdb8d8366e54a3977) 查看更多关于该包的使用信息。

## 使用

- 在 DevTools 的面板中，我们可以看到一个 Profiler 选项
  ![图片](http://p.qpic.cn/qqconadmin/0/edac499b13fc43868619c33e2eca79ba/0)
- 点击 Profiler 选项，中间会有一个 record 的按钮，点击 record 按钮开始录制。
  ![图片](http://p.qpic.cn/qqconadmin/0/1489d2069e77477baa789e8078a7dac0/0)
- 开始录制后，我们可以做一些我们想要记录和分析的操作，然后点击 Stop 按钮来停止录制。
  ![图片](http://p.qpic.cn/qqconadmin/0/cbeaf037b55d4626ae2113e2146fd032/0)

## 性能数据

React 的运作可以分为两个阶段：

- render 阶段：这个阶段用于比较当前的更新和上一次的渲染结果，判断哪些变更需要同步到 DOM 节点。
- commit 阶段：React 在这个阶段确定了最终需要的变更，执行对应的插入、更新、删除等 DOM 操作。React 也在这个阶段调用 componentDidMount 和 componentDidUpdate 等生命周期函数。

DevTools 工具正是对 commit 阶段的性能数据进行收集并进行分组，我们后面得到性能信息也是来源于该阶段。

### 查看 commits

我们可以在 Profiler 标签顶部的看到相关 commit:

![图片](http://p.qpic.cn/qqconadmin/0/b2271831f98f41aeb1636cd22137f0c6/0)

上图中包含 12 组 commit，它类似一个柱状图，每一根柱子代表一次commit，其颜色和高度则对应执行时长，越高颜色越黄代表时间越长，反之越短。同时，点击左右两侧的箭头可以切换成别组的 commit。

### 过滤 commits

我们可以过滤掉一些耗时较短的 commit，以帮助我们更好的分析处理，如下：

![图片](http://p.qpic.cn/qqconadmin/0/7f1ae7039bd54631bd7ce6960d701906/0)

### 火焰图（Flame chart）

commit 下的性能信息默认展示成火焰图。火焰图展示了一次 commit 中每个组件的渲染信息。类似分组的 commit，颜色和长短对应组件的渲染耗时，灰色则代表该组件在此次 commit 中未被渲染。需要注意，每个组件的耗时包含其子组件的耗时，所以我们看到耗时最长的组件往往是最顶层的父组件。

![图片](http://p.qpic.cn/qqconadmin/0/0d5c3cf32b0c4c33a6e11a34ad9f307e/0)

我们可以点击每一个组件来查看它的详细信息，包含其 props 和 state：

![图片](http://p.qpic.cn/qqconadmin/0/b8b51e58974149ae939dde16415be3e8/0)

当我们在选中一个组件的时候切换 commit 分组，我们可以在组件的详细信息面板看到该组件内容变化的提示：

![图片](http://p.qpic.cn/qqconadmin/0/94ce910fb40344bab276b2dd5d3c7b93/0)

### 排序图（Ranked chart）

排序图的选项位于火焰图的右侧，该视图对一次 commit 中变化的组件根据渲染时长进行了排序，耗时最长的组件将排在顶部。

排序图和火焰图一致，同样可以点击组件查看相关的详细信息。

### 组件图（Component chart）

有时候，我们需要查看特定组件在这一次录制中渲染的次数及对应的耗时，我们可以查看其对应的组件图。

![图片](http://p.qpic.cn/qqconadmin/0/bbc2ef48a4a947aaa86d0eb870be3dfb/0)

如上，List 组件在这一次录制中被渲染了 11 次，且它的颜色表明，它也是每一次 commit 中耗时最长的组件。

我们可以双击组件，或者通过选择组件，点击右上角的柱状图图标来打开对应的组件图，点击 x 可以返回上一个图表，如下：

![图片](http://p.qpic.cn/qqconadmin/0/e860e0bf0f5940e9b0c34cb980258314/0)

打开一次 commit 中未被渲染的组件图则会如下显示：

![图片](http://p.qpic.cn/qqconadmin/0/c1bc26a6d16a4cdcac7796903025d173/0)

### 交互（Interactions）

React 最近添加了一个 [实验性的 Api](https://gist.github.com/bvaughn/8de925562903afd2e7a12554adcdda16)，用于追踪更新的原因，如下:

![图片](http://p.qpic.cn/qqconadmin/0/1b3230a44847480d8bb9b771d7b87aba/0)

上图追踪了四个交互（Interactions），每一行交互中的绿点代表对应提交的 commit。
同时的，我们也可以在火焰图或者排序图中看到对应的交互信息：

![图片](http://p.qpic.cn/qqconadmin/0/ecc7ace7dba7456ca32c3b9eeb85a2dc/0)

可以通过单击它们，在 interaction 和 commits 之间互相切换：

![图片](http://p.qpic.cn/qqconadmin/0/4351c16f294948fe950ee84460858b97/0)

### END

这个工具对于性能优化十分有用，我们可以通过这个工具发现并优化项目中耗时较长的 commit，同时可以借助它避免一些无关紧要的 commit。
更多相关的内容可以查看 [官方文档](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)。