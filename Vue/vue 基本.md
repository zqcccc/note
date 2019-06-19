## {{}} vs v-text vsv-html

在网络比较差的时候，v-text 没有文字，双花括号可能就是代码的那个样子，所以，只是文字的时候可以尽量使用 v-text

v-html 可以识别字符串中的 html 标签，解析并插入到页面的 dom 树中，因此v-html 很容易留下 xss 攻击的后门，所以在使用 v-html 的时候要格外小心

## 属性绑定

`v-bind` 或者 `:`

## 方法绑定

在实例的 methods 中添加方法，然后调用 this 可以访问到实例上的 data 和 methods，

如果这里用的是箭头函数的话就会指向 window，因为 箭头函数自身没有 this 的，它的 this 依赖于外部的执行环境

## 计算属性

写在 computed 中，跟发类似，但是只要依赖项不变，就只会执行一次

## 侦听器

watch 里写属性名称，一个名字对应一个对象，对象上的 handle 对应执行的函数，接收 newValue 和 oldValue 两个参数，immediate 代表是否在数据初始化的时候去执行一遍 handle，默认侦听是浅比较，如果 deep 为 true 就会进行深比较

实例方法 `$watch` ，第一个参数为侦听属性的名字（字符串），第二个参数是个侦听的回调函数，第三个参数就是接收一个对象，里面都是配置选项，然后会返回一个 `unwatch` 的函数，这个函数是用来销毁侦听器的函数

## 计算属性和侦听器的差异

计算属性会代理到对应的 Vue 实例上（如 vm.a），而侦听器不会

计算属性会缓存上一次执行的结果，所以可以减少函数执行次数优化性能

vue 推荐 computed 里只写同步代码，watch 更偏向于耗时过程，所以更适合异步

computed 和 watch 其实都是用 Watcher 类实例化的得到的，不过区别是，computed 是以第二个参数传入的，watch 是以第三个参数传入的

## 样式绑定

`v-bind:class` 接受一个对象，也可以放一个 computed 中的属性，同时有静态的属性可以直接写在 `class` 上，`classs` 和  `:class` 可以共存

`:style` 也可以动态地接受一个对象，或者对象名称的数组

## 逻辑渲染

### v-if

`v-if` 和 `v-else` 在渲染同一种元素的时候，当条件变化的时候，vue 会去缓存当前的渲染实例，不会从头开始渲染，如果需要从头渲染，就给它们加上不同 `key` 就完事了

### v-show

v-if 是通过动态增删 dom 节点，v-show 是通过 css 的 display 来显示的，所以 v-if 成本可能高一点

v-show 对 template 是失效的，所以控制多个元素显隐的话还是用 v-if 比较好

结论：在初始化后不再变化的时候用 v-if，频繁切换状态的用 v-show

## 遍历

遍历数组

```html
<ul>
    <li v-for="item in arr">{{item}}</li>
</ul>
<ul>
    <li v-for="(item, index) in arr">{{item}}</li>
</ul>
```

遍历对象

```html
<ul>
    <li v-for="value in obj">{{value}}</li>
</ul>
<ul>
    <li v-for="(value, key, index) in obj">{{value}}</li>
</ul>
```

## 数组更新

vue 将数组的方法劫持了，并对其进行了一些封装，这些方法都对原数组进行了修改

- push
- splice
- pop
- unshift
- sort
- shift
- reverse

下面的就没有进行封装，直接使用是不会触发视图更新的

- vm.arr.filter
- vm.arr.concat
- vm.arr.slice
- **vm.arr[i] = 'xxx'**

即使 vue 的数组进行了重新赋值，但它也不是直接全部进行全量替换，它是启发式的替换

## v-on

在传递事件对象的时候在函数参数里传递 `$event` 就可以了

v-on 可以简写成 @

### 事件修饰符

`.stop` 阻止事件冒泡

`.self` 当触发事件的对象是自身才执行

`.prevent` 阻止浏览器的默认行为（阻止默认行为其实也在回调里面调用 event.preventDefault）

`.capture` 监听器采用事件捕捉模型（原本默认是冒泡模型）

`.once` 监听器触发一次后移除

`.passive` 告诉浏览器该监听器是“主动地”（根据我的理解就是回调里一定不会 preventDefault，直接去触发默认浏览器行为，这样可以解决一些卡顿问题，因为，函数调用总归有成本的，它可能会等到函数执行完才去执行浏览器默认行为，有可能会有卡顿的可能）

## 数据双向绑定

v-model 自定义组件也可以用，只要有一个`props`是`value`，并且在某个事件中调用`this.$emit('input', value)`即可，可以加后缀来实现不同的效果

输入框加了 `.lazy` 要失焦才会触发

组件利用`v-bind.sync="prop"`，进行双向绑定时，需要组件触发`update:prop`事件。

## 表单控件

input  checkbox控件默认选中为 true，不选中为 false

true-value 和 false-value 可以是选中和不选中的自定义值

也可以绑定一个数组，和多个值多选

也可以绑定单选的值

文件上传的控件不支持 v-model

```js
function (evt) {
    this.file = evt.target.files[0]
}
```

## 组件开发

组件大小看开发者的划分力度

### 组件注册

全局注册

```js
Vue.component('my-button', {
    data: function (){
        return {
            count: 0
        }
    },
    template: '<button @click="handleClick">{{count}}</button>',
    methods: {
        handleClick: function() {
            this.count += 1
        }
    }
})
```

局部注册

在单个组件里面有个 components 的项，

## props 传参

配置对象里有个 props，是个数组， 参数名字的字符串

props 也可以是个对象，key 就是传参的名称，value 就是数据类型 `Number` 这种，value 也可以是个对象 `{type: Number, default: 5, validator: function (value) { return value > 0 && value < 10}}`

不用 v-bind 传递都是字符串

vue 中的 props 也是遵循单向数据流的















## vue 基本概念

- 全局 api
- 实例选项
- 实例属性/方法
- 指令
- 内置组件

## 基本用法



- 单文件组件

- 文本渲染 `v-html` `{{}}` `v-text` 

- 列表渲染 数组 对象

- 列表数据的同步更新

- `v-bind` 动态绑定属性

- `v-bind` 可以简写为 `:`

- 使用 `v-bind` 绑定 **class 和内联样式**

- 使用 `v-if` `v-show` `v-else` 进行条件渲染

- `v-on` `@` `methods` 事件绑定

  - 在组件中，方法不要用箭头函数，不然 this 不能直接访问到组件实例的属性
  - [官方说明 methods](https://cn.vuejs.org/v2/api/#methods)

> 注意，**不应该使用箭头函数来定义 method 函数** (例如 `plus: () => this.a++`)。理由是箭头函数绑定了父级作用域的上下文，所以 `this` 将不会按照期望指向 Vue 实例

- `v-on 修饰符` 可以指定键盘事件

### Class 与 Style 绑定

[官方说明](https://cn.vuejs.org/v2/guide/class-and-style.html)

### v-if 和 v-show

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

> 注意，`v-show` 不支持 `<template>` 元素，也不支持 `v-else`。
>
> **不推荐**同时使用 `v-if` 和 `v-for`。请查阅[风格指南](https://cn.vuejs.org/v2/style-guide/#%E9%81%BF%E5%85%8D-v-if-%E5%92%8C-v-for-%E7%94%A8%E5%9C%A8%E4%B8%80%E8%B5%B7-%E5%BF%85%E8%A6%81)以获取更多信息。

当 `v-if` 与 `v-for` 一起使用时，`v-for` 具有比 `v-if` 更高的优先级。请查阅[列表渲染指南](https://cn.vuejs.org/v2/guide/list.html#v-for-with-v-if) 以获取详细信息。

### `v-model` 表单数据模型双向绑定

- 你可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。

> `v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` 特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 `data` 选项中声明初始值。

`v-model` 在内部使用不同的属性为不同的输入元素并抛出不同的事件：

- text 和 textarea 元素使用 `value` 属性和 `input` 事件；
- checkbox 和 radio 使用 `checked` 属性和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

> 如果 `v-model` 表达式的初始值未能匹配任何选项，`<select>` 元素将被渲染为“未选中”状态。在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 change 事件。因此，更推荐像上面这样提供一个值为空的禁用选项。

### 提升性能：v-pre

- 说明：vue会跳过这个元素和它的子元素的编译过程。可以用来显示原始 Mustache 标签。跳过大量没有指令的节点会加快编译。

```html
<span v-pre>{{ this will not be compiled }}</span>
```

### 提升性能：v-once

- 说明：vue只渲染元素和组件一次。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。

```html
<span v-once>This will never change: {{msg}}</span>
```

### 监视数据变化 - watch

- 概述：`watch`是一个对象，键是需要观察的表达式，值是对应回调函数
- 作用：当表达式的值发生变化后，会调用对应的回调函数完成响应的监视操作
- [VUE $watch](https://cn.vuejs.org/v2/api/#vm-watch)

```
new Vue({
  data: { a: 1, b: { age: 10 } },
  watch: {
    a: function(val, oldVal) {
      // val 表示当前值
      // oldVal 表示旧值
      console.log('当前值为：' + val, '旧值为：' + oldVal)
    },

    // 监听对象属性的变化
    b: {
      handler: function (val, oldVal) { /* ... */ },
      // deep : true表示是否监听对象内部属性值的变化 
      deep: true
    },

    // 只监视user对象中age属性的变化
    'user.age': function (val, oldVal) {
    },
  }
})
```

## 计算属性

- 说明：**计算属性是基于它们的依赖进行缓存的**，只有在它的依赖发生改变时才会重新求值，相比之下，每当触发重新渲染时，调用方法将**总会**再次执行函数。
- 注意：Mustache语法（{{}}）中不要放入太多的逻辑，否则会让模板过重、难以理解和维护
- 注意：**computed中的属性不能与data中的属性同名，否则会报错**
- [Vue computed属性原理](<http://www.cnblogs.com/kidney/p/7384835.html?utm_source=debugrun&utm_medium=referral>)

```js
var vm = new Vue({
  el: '#app',
  data: {
    firstname: 'jack',
    lastname: 'rose'
  },
  computed: {
    fullname() {
      return this.firstname + '.' + this.lastname
    }
  }
})
```

## 实例生命周期

- 所有的 Vue 组件都是 Vue 实例，并且接受相同的选项对象即可 (一些根实例特有的选项除外)。
- 实例生命周期也叫做：组件生命周期

### 生命周期介绍

- [vue生命周期钩子函数](https://link.juejin.im/?target=https%3A%2F%2Fcn.vuejs.org%2Fv2%2Fapi%2F%23)
- 简单说：**一个组件从开始到最后消亡所经历的各种状态，就是一个组件的生命周期**

生命周期钩子函数的定义：从组件被创建，到组件挂载到页面上运行，再到页面关闭组件被卸载，这三个阶段总是伴随着组件各种各样的事件，这些事件，统称为组件的生命周期函数！

- 注意：Vue在执行过程中会自动调用`生命周期钩子函数`，我们只需要提供这些钩子函数即可
- 注意：钩子函数的名称都是Vue中规定好的！

![官方生命周期图示](https://cn.vuejs.org/images/lifecycle.png)

### 钩子函数 - beforeCreate()

- 说明：在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用
- 注意：此时，无法获取 data中的数据、methods中的方法

### 钩子函数 - **created()**

- 注意：这是一个常用的生命周期，可以调用methods中的方法、改变data中的数据
- [vue实例生命周期 参考1](https://link.juejin.im/?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000008879966)
- [vue实例生命周期 参考2](https://link.juejin.im/?target=https%3A%2F%2Fsegmentfault.com%2Fa%2F1190000008010666)
- 使用场景：发送请求获取数据


> 通常我们可以在这里对实例进行预处理。
> 也有一些童鞋喜欢在这里发 ajax 请求，值得注意的是，这个周期中是没有什么方法来对实例化过程进行拦截的。
> 因此假如有某些数据必须获取才允许进入页面的话，并不适合在这个页面发请求。
> 建议在组件路由勾子 beforeRouteEnter 中来完成。


### 钩子函数 - beforeMounted()

- 说明：在挂载开始之前被调用

### 钩子函数 - **mounted()**

- 说明：此时，vue实例已经挂载到页面中，可以获取到el中的DOM元素，进行DOM操作


> 1. 在这个周期内，对data的改变可以生效。但是要进下一轮的 dom 更新，dom 上的数据才会更新。
> 2. 这个周期可以获取 dom。
> 3. beforeRouteEnter 的 next 的勾子比 mounted 触发还要靠后
> 4. 指令的生效在 mounted 周期之前


### 钩子函数 - beforeUpdated()

- 说明：数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。你可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。
- 注意：此处获取的数据是更新后的数据，但是获取页面中的DOM元素是更新之前的

### 钩子函数 - updated()

- 说明：组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。

### 钩子函数 - beforeDestroy()

- 说明：实例销毁之前调用。在这一步，实例仍然完全可用。
- 使用场景：实例销毁之前，执行清理任务，比如：清除定时器等

### 钩子函数 - destroyed()

- 说明：Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

## 组件

> 组件系统是 Vue 的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。仔细想想，几乎任意类型的应用界面都可以抽象为一个组件树

- 创建组件的两种方式：1 全局组件 2 局部组件

### 全局组件

- 说明：全局组件在所有的vue实例中都可以使用
- 注意：**先注册组件，再初始化根实例**
- `extend`：使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。

### 局部组件

- 说明：局部组件，是在某一个具体的vue实例中定义的，只能在这个vue实例中使用

### is特性

> 在某些特定的标签中只能存在指定表恰 如ul > li 如果要浏览器正常解析则需要使用is








- 使用 `组件数` 设计项目，配置文件链接各个组件 命名转换 动态组件

过渡

- CSS 过渡
- JS 过渡

自定义指令

mixins

插件