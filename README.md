# 个人学习笔记

计算机学科基础 - 数据结构和算法

- 数据结构
  - 哈希表
  - 优先队列
  - 后缀数组
  - 红黑树
  - 线段树
  - 链表
  - 栈
  - 二叉树
  - 图
- 算法
  - DFS、BFS
  - 回溯法
  - 排序
  - 二分搜索
  - DP
  - 贪心
  - KMP

## 目录

建设中...

## 掌握科班必备知识

- c 语言
  - 所有的语言都有 c 语言的影子
- 计算机网络
  - 网站底层性能优化
  - HTTP2 与 1 的区别
- 数据结构与算法
  - 通向高阶前端的必经之路
- Linux 基本操作
  - 服务器一般都是 Linux
  - git 等，多去敲命令
  - windows cmder 虚拟终端
- 网站架构基本原理
  - nginx

## 前端基础

- HTML
  - 如何优化 SEO
- CSS
  - Render Layer
  - Compositing Layer
  - 硬件加速
  - 底层
- JS
  - 熟悉 V8 异步机制
    - 微任务 宏任务
  - 用 ES5 手写 ES6

## 其他维度

- 服务端尝试
  - Node.js
  - Nginx
- 移动端
  - H5+
  - AMP
  - PWA
  - ServiceWorker
- 前端工程化
  - 项目架构，脚手架的沉淀
  - 自动化测试
- 前端图形学
  - Canvas
  - WebGL
  - AR
- 面向未来
  - IoT
  - AI

## 获取新知识的途径

- 自媒体时代下这不应该成为一个问题
- 书籍
  - 看原理的书不容易过时，工具类的书籍是比较容易过时
- YouTube、GitHub & Stack Overflow

## 面经

- 多写总结，多做开源
- 整理一份属于你自己只是体系的自我介绍，并实时更新它
- STAR 法则
- 勇于尝试，面试是一个经验积累的过程
- 硬实力永远是手中的话语权

# 前端面试题目 top15 系列

前端面试题有很多，真要一个个得去刷的话，估计三天三夜都刷不完，不过要考的知识点总是那些（当然随着新技术或框架的出现，会新增一些）。这里为大家精选了 15 道经典的前端面试题（题目不分先后），供大家练练手，顺便也可以查漏补缺。

## 说一下你对 CSS 盒模型的理解？

- 盒模型包括的属性
- box-sizing

## 对于一个未知宽高的盒子，如何让它水平垂直居中于父元素？

- table
- JS 计算
- transform
- flexbox
- grid

## 简单说下 flexbox 布局？

- flexbox 布局解决的问题（未出现之前，布局的缺陷）
- flexbox 的兼容性
- flexbox 的局限（引出 grid 布局）

## 说下你对 CSS3 动画的理解？

- transition
- animation
- 动画性能（transform、3D等）

## 简单说下你对一些小图标的处理？

- 雪碧图
- iconfont
- svg sprites

## 浏览器缓存的头字段有哪些，缓存的逻辑是怎样的？

- 缓存
- 引申：200 和 304 的区别

## 创建一个有十行十列的表格（不准 innerHTML），并给每个单元格绑定事件。

- DOM 操作
- 事件委托

## 写个 Person 类，属性 name 公有，属性 age 私有；写个 Student 继承 Person，并有自己的公有属性 grade 和公有方法 getTeacher。

- 面向对象（变量，方法，私有/公有，继承...）

## HTTP 常见的状态码，301，302 等；POST 和 GET 的区别。

- 网络（状态码，请求方法...）

## 有没有碰到过跨域请求，你是怎么处理的？

- 跨域（jsonp，CORS...）

## 写个正则匹配下邮箱、手机号

- 正则

## 接触过哪些构建工具；现在用的是啥；写出你现在项目的目录结构，并解释构建工具的运作方式；有否写过插件？

- 构建（fis3、gulp、webpack...）

## 你写代码的时候是怎么考虑安全因素的？

- 安全（xss、csrf...）

## 给你的代码写过单元测试吗；为了更好地单测你是怎么组织你的代码的；我这里有份代码，你看怎么给每个函数写个单测？

- 单元测试（Mocha...）

## 有没有做过性能优化，都有哪些手段？

- 雅虎35条
