[思维导图](https://www.processon.com/view/link/5d1eb5a0e4b0fdb331d3798c)

## 开始

> ### 术语解释
>
> Runtime: 仅包含运行时的版本，包含 vue 运行核心代码但没有模板编译器，如 vue.runtime.js
>
> umd: Universal Module Definition 规范，用于浏览器 script 标签，默认包含运行时和编译器，如 vue.js
>
> commonjs: cjs 规范用于旧版打包器如 browserify、webpack 1，如 vue.runtime.common.js
>
> esm: ES module 规范用于现代打包工具如 webpack 2 及以上版本 vue.runtime.esm.js
>
> ### Compiler VS Runtime
>
> 带 compiler 版本支持 template 选项，可以实时编译模板
>
> 仅 runtime 版不支持 template，体积小，需要借助 webpack 把 template 解析成 render 函数

1.  调试vue项⽬目的⽅方式 

   - 安装依赖:npm i

   - 安装打包⼯工具:npm i rollup -g 

   - 修改package.json⾥面dev脚本:

     ```
      "dev": "rollup -w -c scripts/config.js --sourcemap --environment TARGET:web-full-dev"
     ```

   - 执行打包

     ```
     npm run dev
     ```

   - npm run dev

2. vue是如何启动的

3. vue响应式机制逐⾏行行分析

整理理启动顺序:

src/platforms/web/entry-runtime-with-compiler.js

```
div#app

new Vue({
  template:dom
}).$mount('#app')
```

src/platforms/web/runtime/index.js

```
import Vue from 'core/index'

Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}
```

src/core/index.js

```js
import Vue from './instance/index'
initGlobalAPI(Vue)
```

- initGlobalAPI

src/core/instance/index.js

构造函数

```js
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)
```

- initMixin(Vue)

  ```
  initLifecycle(vm)
  initEvents(vm)
  initRender(vm)
  callHook(vm, 'beforeCreate')
  initInjections(vm) // resolve injections before data/props
  initState(vm)
  initProvide(vm) // resolve provide after data/props
  callHook(vm, 'created')
  ```

  - initLifecycle: children等 
  - initEvents:事件监听初始化 
  - initRender:定义$createElement

  - initInjections: 获取注⼊入数据并做响应化 
  - initState:初始化props,methods,data,computed,watch等 
  - initProvide:注⼊入数据处理理

- stateMixin(Vue): 实现 set,$delete
- eventsMixin(Vue): 实现 on..
- lifecycleMixin(Vue): 实现 _updata, forceUpdate, destory
- renderMixin(Vue): _render $nextTick

## 数据响应式

src/core/instance/state.js

## diff 相关

patchVnode 

两个VNode类型相同，就执⾏更新操作，包括三种类型操作：属性更新PROPS、⽂本更新TEXT、⼦节 点更新REORDER 

patchVnode具体规则如下：

1. 如果新旧VNode都是**静态**的，同时它们的key相同（代表同⼀节点），并且新的VNode是clone或 者是标记了v-once，那么只需要替换elm以及componentInstance即可。 
2. 新⽼节点**均有children**⼦节点，则对⼦节点进⾏diff操作，调⽤**updateChildren**，这个 **updateChildren也是diff的核⼼**。
3. 如果**⽼节点没有⼦节点⽽新节点存在⼦节点**，先清空⽼节点DOM的⽂本内容，然后为当前DOM节 点加⼊⼦节点。 
4. 当**新节点没有⼦节点⽽⽼节点有⼦节点**的时候，则移除该DOM节点的所有⼦节点。 
5. 当**新⽼节点都⽆⼦节点**的时候，只是⽂本的替换。