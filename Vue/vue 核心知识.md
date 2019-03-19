## 声明一个组件

1. 全局
   - new Vue() 
   - const component  = {template: '<div>hahah</div>'} 
   - Vue.component()

data 得是一个 function 返回需要的值，否则引用了相同的数据的话，一个变大家全变

props 指定组件可配置的一些项，数据要规定类型

v-bind 可以解析绑定的值

js 可以写定义一个变量 propOne，  template 里的是 html 所以标签是 <prop-one></prop-one> 

推荐 props 是单向数据流，不然父子组件都可以改变值会造成混乱

props 里 validator (value) {reutrn ...} 返回一个 boolean 值

2. [Vue.extend( options )](https://cn.vuejs.org/v2/api/#Vue-extend)

## 双向绑定

自定义事件也可以用于创建支持 `v-model` 的自定义输入组件。记住：

```html
<input v-model="searchText">
```

等价于：

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

v-model

mode   $emit

## 高级功能

[slot](https://cn.vuejs.org/v2/api/#slot) 用于标记往哪个具名插槽中插入子组件内容

slot-scope 作用域插槽

provide inject 跨级组件沟通 Object.defineProperty() 可以让数据双向绑定

## render

render() 会 return this.$createElement