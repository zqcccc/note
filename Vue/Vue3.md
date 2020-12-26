# Vue 3.0

- 源码组织方式
  - Typescript
  - Monorepo
- Composition API
- 性能提升
- Vite

## 不同构建版本

去除了 umd 的方式

脚手架默认是用最小的 vue.runtime.esm-bundler.js，没有打包所有的代码，要配合打包工具使用，在构建打包的时候只会打包用到的东西，这样构建出来的包就很小

## Composition 简单介绍

### 动机

主要是 Options API 在应用组件越来越大的情况下，同一个功能相关的代码要找来找去，且不方便复用，即使用 mixin 复用也存在命名冲突的问题，变量和方法的混入来源很不方便查看

Options API

- 包含一个描述组件选项（data、methods、props等）的对象
- Options API 开发复杂组件，统一功能逻辑的代码被拆分到不同的选项

Composition API

就是解决代码逻辑分散的问题，也更方便复用了，更多的感慨是，和 hooks 好像啊

## 性能

- 响应式系统
  - Proxy
    - 可以监听动态新增的属性
    - 可以监听删除的属性
    - 可以监听数组的索引和 length 属性
- 编译优化
  - 编译过程
    - Vue2通过标记静态根节点，优化 diff 的过程
    - Vue3中标记和提升所有的静态根节点，diff的时候只需要对比动态节点内容
      - Fragments
      - 静态提升
        - 只在初始化 render 一遍
      - Patch flag 虚拟 DOM
        - 不同的 flag 代表patch更新时只需要检查特定的内容
      - 缓存事件处理函数
- 源码体积优化
  - 移除了 inline-template、filter 等不常用的 api
  - tree-shaking

## Vite

现代浏览器都支持 ES Module

```html
<script type="module" src="..." ></script>
```

支持模块 script 默认延迟加载

- 类似于 script 标签设置 defer
- script 执行是在文档解析完成后（可以拿到 DOM），触发 DOMContentLoaded 事件前执行

vite 快就是利用浏览器下可以使用 es module，从而避免开发环境下打包来提升开发效率

- vite 在开发模式下不需要打包可以直接运行
- vue-cli 开发模式下必须对项目打包才可以运行

vite 特点

- 快速冷启动
- 按需编译
- 模块热更新

vite 在生产模式下使用 rollup 打包

- 基于 ES Module 的方式打包

- html 加载的是 xxx.vue 文件，但是其实开发服务器在收到这样的请求的时候，会去编译 xxx.vue 文件，然后返回，Content-Type 其实是 `application/javascript`，所以执行的还是 js
- 入口引入的 xxx.vue 会继续去引入需要的模块 xxx.vue 开发服务器会再编译 vue 文件

## Composition

composition 仅仅是 vue 3 新增的 api，以前的 options 也还是可以用的

option 中的 data 不再支持对象写法，只能写函数返回对象

**要使用 composition api 的话要在 options 中使用 setup 函数**，会在 beforeCreate 和 created 之间执行，不过也能访问到全生命周期

这里有个需要注意的主要是 `useXXX()` 返回的代理对象不能结构赋值，解构会直接触发 getter 拿到值后就复制值而已，后面再用这些复制的值是不会触发 getter 和 setter 的，所以就不能响应式了

不过可以使用 `toRefs(/*需要解构的对象*/)` 包裹一遍就可以在外面解构取值了，因为**toRefs 把一个对象里的所有属性的值都变成响应式的对象然后挂载到原来的属性上**了（传给 toRefs 的对象首先得是代理对象）

不过结构出来的值在代码中使用的时候要通过 `.value` 去访问对象的值，不能直接使用（template模板里可以直接使用）

**ref** 和 **toRefs** 类似，都可以创建一个响应式的对象，不过如果传入的是基本类型的话就是创建一个具有 value 的 key 的对象，这个对象有 getter 和 setter；如果传入的是对象，其实和调用 reactive 是一样的

**computed** 用来缓存计算结果，依赖不变就不会执行，这个方法会返回一个不可变对象，类似 ref，可以通过 getter 和 setter 对值进行修改 value

**watch**

- 第一个参数：要监听的数据
- 第二个参数：监听到数据变化后执行的函数，这个函数有两个参数分别是新值和旧值
- 第三个参数：选项对象，deep 和 immediate

返回值是取消监听的函数

**watchEffect** 监听的值变了就会再执行一遍

## 响应式系统

- Proxy 对象实现属性监听
- 多层属性嵌套，在访问属性过程中处理下一级属性
- 默认监听动态添加的属性
- 默认监听属性的删除操作
- 默认监听数组索引和 length 属性
- 可以作为单独的模块使用

### Proxy

```javascript
'use strict'
// set 和 deleteProperty 中需要返回布尔类型的值
// 在严格模式下，如果返回 false 的话会出现 Type Error 的异常
const target = {
  foo: 'xxx',
  bar: 'yyy'
}
// Reflect.getPrototypeOf()
// Object.getPrototypeOf()
const proxy = new Proxy(target, {
  get (target, key, receiver) {
    // return target[key]
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    // target[key] = value
    return Reflect.set(target, key, value, receiver)
  },
  deleteProperty (target, key) {
    // delete target[key]
    return Reflect.deleteProperty(target, key)
  }
})

proxy.foo = 'zzz'
// delete proxy.foo
```

receiver

```js
// Proxy 中 receiver：Proxy 或者继承 Proxy 的对象
// Reflect 中 receiver：如果 target 对象中设置了 getter，getter 中的 this 指向 receiver

const obj = {
  get foo() {
    console.log(this)
    return this.bar
  }
}

const proxy = new Proxy(obj, {
  get (target, key, receiver) {
    if (key === 'bar') { 
      return 'value - bar' 
    }
    return Reflect.get(target, key, receiver)
  }
})
console.log(proxy.foo) // 'value - bar'
```

### reactive

- 接受一个参数，判断参数不是对象直接返回，是对象就转换成代理对象
- 创建拦截器对象 handler，设置 get/set/deleteProperty
  - get 中收集依赖
  - set 和 deleteProperty 中去触发更新
- 返回 Proxy 对象

### 收集依赖

创建一个 weakMap 叫 targetMap，key 是目标对象，value 是 depsMap ，是正常的 Map

depsMap 中 key 是目标对象的属性名称，value 是 dep，是 set 类型，里面是 effect 的函数

收集依赖就是在创建 weakMap -> Map -> Set 的一个关系，

其中第一个 targetMap 用来记录目标对象和一个字典 depsMap

### ref

- 可以把基本类型数据转成响应式对象
- ref 返回的对象，重新赋值成对象也是响应式的
- reactive 返回的对象，重新赋值丢失响应式
- reactive 返回的对象不能解构赋值

### toRefs

接受一个 proxy 对象，我们这里一般就是和 reactive 函数一起使用

将属性都变成一个可以拿到原来 proxy 对象对应 key 的值的新对象

### computed

接受一个函数 getter 作为参数

在 effect 中调用参数函数，并把结果给到 ref 创建的对象的 value 上

最后把刚创建的 ref 对象返回

这个过程中，effect 传入了我们需要收集的函数，其中执行了 getter 函数并把结果赋值返回的操作，会被 getter 中的 reactive 过的对象访问过程中收集，这样下次 reactive 对象变化就会触发这个刚刚收集起来的函数，这就完成了以前的计算属性

```js
// 一些工具函数
const isObject = val => val !== null && typeof val === 'object'
const convert = target => isObject(target) ? reactive(target) : target
const hasOwnProperty = Object.prototype.hasOwnProperty
const hasOwn = (target, key) => hasOwnProperty.call(target, key)

export function reactive (target) {
  if (!isObject(target)) return target

  return new Proxy(target, {
    get (target, key, receiver) {
      // 收集依赖
      track(target, key)
      const result = Reflect.get(target, key, receiver)
      return convert(result)
    },
    set (target, key, value, receiver) {
      const oldValue = Reflect.get(target, key, receiver)
      let result = true
      if (oldValue !== value) {
        result = Reflect.set(target, key, value, receiver)
        // 触发更新
        trigger(target, key)
      }
      return result
    },
    deleteProperty (target, key) {
      const hadKey = hasOwn(target, key)
      const result = Reflect.deleteProperty(target, key)
      if (hadKey && result) {
        // 触发更新
        trigger(target, key)
      }
      return result
    }
  })
}

let activeEffect = null
export function effect (callback) {
  activeEffect = callback
  callback() // 访问响应式对象属性，去收集依赖
  activeEffect = null // 依赖如果有嵌套需要递归收集
}

let targetMap = new WeakMap() // 依赖全部收集在这里

export function track (target, key) {
  if (!activeEffect) return
  let depsMap = targetMap.get(target)
  if (!depsMap) {
    targetMap.set(target, (depsMap = new Map()))
  }
  let dep = depsMap.get(key)
  if (!dep) {
    depsMap.set(key, (dep = new Set()))
  }
  dep.add(activeEffect) // 回调都会放 set 中
}

export function trigger (target, key) {
  const depsMap = targetMap.get(target)
  if (!depsMap) return
  const dep = depsMap.get(key)
  if (dep) {
    dep.forEach(effect => {
      effect()
    })
  }
}

export function ref (raw) {
  // 判断 raw 是否是ref 创建的对象，如果是的话直接返回
  if (isObject(raw) && raw.__v_isRef) {
    return
  }
  let value = convert(raw)
  const r = {
    __v_isRef: true, // 标记 ref 
    get value () {
      track(r, 'value')
      return value
    },
    set value (newValue) {
      if (newValue !== value) {
        raw = newValue
        value = convert(raw)
        trigger(r, 'value')
      }
    }
  }
  return r
}

export function toRefs (proxy) {
  const ret = proxy instanceof Array ? new Array(proxy.length) : {}

  for (const key in proxy) {
    ret[key] = toProxyRef(proxy, key) // 将属性都变成一个可以拿到原来 proxy 对象对应 key 的值的新对象
  }

  return ret
}

function toProxyRef (proxy, key) {
  const r = {
    __v_isRef: true,
    get value () {
      return proxy[key]
    },
    set value (newValue) {
      proxy[key] = newValue
    }
  }
  return r
}

export function computed (getter) {
  const result = ref()

  effect(() => (result.value = getter()))

  return result
}
```

