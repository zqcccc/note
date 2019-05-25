# Sass 资源参考

Sass 作为目前最受欢迎的 CSS 预处理器，学习参考资源也是非常的多，语法入门其实也比较简单，常用的我们都已经介绍过了，更深入的了解可以参考如下教程：

- [Sass guide](https://www.w3cplus.com/sassguide/)
- [Sass 官网](http://sass-lang.com/)
- [Sass 中文文档](http://sass.bootcss.com/docs/sass-reference/)
- [Sass 相关教程](http://www.w3cplus.com/blog/tags/302.html)

## Sass 安装与编译

对于新手来说，Sass 的安装与编译是必然会遇到的一个大问题，一不小心就会让你心灰意冷。不过没关系，耐心点，前人都已经准备好了一摞经验。

### ruby sass

首先，Sass 是用 ruby 写的，所以你可以使用 ruby 来编译。

具体的安装与编译可参考：[Sass 安装](https://www.w3cplus.com/sassguide/install.html)

这里的主要问题是因为国内网络墙得比较厉害，所以一般安装过程都不会那么顺利。这时就可以选择翻墙，或者使用淘宝RubyGems镜像安装等

注：因为 ruby 的编译速度实在不怎么的，所以在项目中我们一般选择下面的 libsass。

### libsass

[libsass](http://sass-lang.com/libsass) 是一个用 C 语言实现的 Sass 解析器。特点是简单、速度快而且易于集成。但是 libsass 只是一个库，并不能直接使用，如果要实际使用还需要一个外壳来进行包装，如PHP，Node等

对于前端来说，优先选择的当然是用 Node 包裹的 [node-sass](http://sass-lang.com/libsass)。

同样由于网络被墙，使用 npm 安装 node-sass 失败率也会非常高，这时你可以选择翻墙，或者自己手动下载对应的
[node sass binaries](https://github.com/sass/node-sass-binaries)放到对应的目录中。

注：以后我们介绍构建时会介绍如何配置自动化 node-sass 编译，对于大项目来说，该方案才是最优选择。

### 其他编译

- 编辑器编译：一般来说，编辑器都会有相应的 Sass 编译插件，或默认就自带了 Sass 编译功能
- 在线编译：[Sass meister](https://www.sassmeister.com/)
- 图形化编译工具：[Koala](http://koala-app.com/index-zh.html)
- 构建自动化编译：[node-sass](http://sass-lang.com/libsass)

注：如果你是一个完全的前端新手，建议使用 [Koala](http://koala-app.com/index-zh.html) 或编辑器插件来编译或直接在线编译。等学完了构建课程之后，就可以随便选择你喜欢的方式进行编辑。