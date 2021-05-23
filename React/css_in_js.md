# css in js

## 为什么会有 css in js

CSS-IN-JS 是 WEB 项目中将 CSS 代码捆绑在 JavaScript 代码中的解决方案. 

这种方案旨在解决 CSS 的局限性, 例如缺乏动态功能, 作用域和可移植性.

## 介绍

CSS-IN-JS 方案的优点:

1. 让 CSS 代码拥有独立的作用域, 阻止 CSS 代码泄露到组件外部, 防止样式冲突.
2. 让组件更具可移植性, 实现开箱即用, 轻松创建松耦合的应用程序
3. 让组件更具可重用性, 只需编写一次即可, 可以在任何地方运行. 不仅可以在同一应用程序中重用组件, 而且可以在使 用相同框架构建的其他应用程序中重用组件.
4. 让样式具有动态功能, 可以将复杂的逻辑应用于样式规则, 如果要创建需要动态功能的复杂UI, 它是理想的解决方案.

CSS-IN-JS 方案的缺点:

1. 为项目增加了额外的复杂性.
2. 自动生成的选择器大大降低了代码的可读性

## emotion库

### Emotion 介绍

Emotion 是一个旨在使用 JavaScript 编写 CSS 样式的库.

`npm install @emotion/core @emotion/styled`

babel配置

```
"presets": [
	"react-app",
	"@emotion/babel-preset-css-prop"
]
```

写法

```jsx
import styled from '@emotion/styled'
const Button = styled.button`
	width: 100px;
	height: 30px;
	background: ${props => props.bgColor || 'skyblue'};
`
function App() {
  return <Button></Button>
}
```

对象写法

```jsx
const Container = styled.div(
	width: props.w || 1000,
  background: 'pink',
  margin: '0 auto'
)
function App() {
  return <Container w={101}> {// 这个就会覆盖默认的 1000 witdh}
  	<Button></Button>
  </Container>
}
```

还可以写成自动判断 props 有没有传值的

```jsx
const Container = styled.div({
	width: 800,
  background: 'pink'
}, props => ({
  width: props.w,
  background: props.bgColor
}))
```

为任何组件添加样式

```jsx
const Demo = ({ className }) => <div className={className}>Demo</div>
// 返回了一个新的组件
const Fancy = styled(Demo)`
	color: red;
`
```

通过父组件设置子组件的样式

```jsx
const Child = styled.div`
	color: red;
`
const Parent = styled.div`
	${Child} {
    color: green;
  }
`

const Child = styled.div({
  color: 'red'
})
const Parent = styled.div({
  [Child]: {
    color: 'red'
  }
})
```

嵌套选择器 &，& 表示组件本身.

```tsx
const Container = styled.div`
	color: red;
	& > a {
		color: pink;
	}
`
```

