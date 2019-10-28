# JSX 语法规范

[JSX](https://facebook.github.io/jsx/) 是由 Facebook 提出的 ECMAScript 的语法扩展，类似 XML。其最初的目的之一是方便在 React 中直观地书写组件标签，我们需要明确的是，它并不是被设计由引擎或者浏览器来实现的，它本身也并不是 ECMAScript 的一个提案，而是旨在被各种预处理器（转换器）用于将 JSX 语法转换为标准 ECMAScript。

实际上，只要具备 html 的基本知识，学习 JSX 语法则非常简单。

JSX 基本的内容有：

- JSX 元素
- JSX 片段

## JSX 元素

单个 JSX 元素可以采用如下写法：

- `<JSXParent />`
- `<JSXParent></JSXParent>`
- `<JSXParent>Text</JSXParent>`

我们可以在书写 JSX 元素的时候，可以定义属性：

- `<JSXParent attr1='1' />`
- `<JSXParent attr2={var1} />`

如果我们定义的属性是一个变量或者表达式，我们需要使用花括号的形式包裹起来。

## JSX 片段

多个 JSX 元素嵌套可以组成 JSX 片段，如下即为 Facebook 官方提供的一个例子：

```
<Dropdown>
  A dropdown list
  <Menu>
    <MenuItem>Do Something</MenuItem>
    <MenuItem>Do Something Fun!</MenuItem>
    <MenuItem>Do Something Else</MenuItem>
  </Menu>
</Dropdown>
```

另外，我们还可以通过这种方式定义一个片段：

```
<>
  <MenuItem>Do Something</MenuItem>
  <MenuItem>Do Something Fun!</MenuItem>
  <MenuItem>Do Something Else</MenuItem>
</>
```

更详细的 JSX 规则，可以参考[这里](https://facebook.github.io/jsx/)