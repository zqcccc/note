# vue-router

## 安装：

```
vue add router
```

## 起步

配置

```js
// router.js
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/Home.vue'
Vue.use(Router); // 引⼊Router插件
export default new Router({
  mode: "history", // 模式：hash | history | abstract
  base: process.env.BASE_URL, // http://localhost:8080/cart
  routes: [
    {
      path: "/",
      name: "home",
      component: Home
    },
    {
      path: "/about",
      name: "about",
      // 路由层级代码分割，⽣成分⽚(about.[hash].js)
      // 当路由房问时会懒加载.
      component: () =>
        import(
          /* webpackChunkName: "about" */
          "./views/About.vue"
        )
    }
  ]
});
```

指定路由器

```js
// main.js
new Vue({
  router,
  render: (h) => h(App),
}).$mount("#app");
```

路由视图

```vue
<router-view/>
```

导航链接

```vue
<router-link to="/">Home</router-link>
<router-link to="/about">About</router-link>
```

路由嵌套

应⽤界⾯通常由多层嵌套的组件组合⽽成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层 组件。 

配置嵌套路由，router.js

```
{
 path: "/",
 component: Home,
 children: [{ path: "/list", name: "list", component: List }]
}
```

⽗组件需要添加插座，Home.vue

```vue
<template>
  <div class="home">
    <h1>⾸⻚</h1>
    <router-view></router-view>
  </div>
</template>
```

## 动态路由

我们经常需要把某种模式匹配到的所有路由，全都映射到同⼀个组件。 

详情⻚路由配置，router.js

```vue
{
     path: "/",
     component: Home,
     children: [
         { path: "", name: "home", component: List },
         { path: "detail/:id", component: Detail },
     ]
}
```

跳转，List.vue

```vue
<ul>
 	<li><router-link to="/detail/1">web全栈</router-link></li>
</ul>
```

获取参数，Detail.vue

```vue
<template>
     <div>
         <h2>商品详情</h2>
         <p>{{$route.params.id}}</p>
     </div>
</template>
```

## 路由守卫

路由导航过程中有若⼲⽣命周期钩⼦，可以在这⾥实现逻辑控制。 

全局守卫，router.js

```js
// 路由配置
{
  path: "/about",
  name: "about",
  meta: { auth: true }, // 需要认证
  component: () => import(/* webpackChunkName: "about" */ "./views/About.vue"),
};

// 守卫
router.beforeEach((to, from, next) => {
  // 要访问/about且未登录需要去登录
  if (to.meta.auth && !window.isLogin) {
    if (window.confirm("请登录")) {
      window.isLogin = true;
      next(); // 登录成功，继续
    } else {
      next("/"); // 放弃登录，回⾸⻚
    }
  } else {
    next(); // 不需登录，继续
  }
});

```

路由独享守卫

```js
// 使用了 vue-router 之后就有了组件里就有了这个生命周期钩子
beforeEnter(to, from, next) {
  // 路由内部知道⾃⼰需要认证
  if (!window.isLogin) {
    // ...
  } else {
    next();
  }
}
```

组件内的守卫

```js
export default {
 beforeRouteEnter(to, from, next) {},
 beforeRouteUpdate(to, from, next) {},
 beforeRouteLeave(to, from, next) {}
};
```

## vue-router拓展

### 动态路由

利⽤$router.addRoutes()可以实现动态路由添加，常⽤于⽤户权限控制。

```js
// router.js
// 返回数据可能是这样的
//[{
// path: "/",
// name: "home",
// component: "Home", //Home
//}]
// 异步获取路由
api.getRoutes().then((routes) => {
  const routeConfig = routes.map((route) => mapComponent(route));
  router.addRoutes(routeConfig);
});
// 映射关系
const compMap = {
  Home: () => import("./view/Home.vue"),
};
// 递归替换
function mapComponent(route) {
  route.component = compMap[route.component];
  if (route.children) {
    route.children = route.children.map((child) => mapComponent(child));
  }
  return route;
}

```

### ⾯包屑

利⽤$route.matched可得到路由匹配数组，按顺序解析可得路由层次关系。

```js
// Breadcrumb.vue
  watch: {
        $route: {
          handler(route) {
            console.log(this.$route.matched);
            this.crumbData = this.$route.matched.map(
              (m) => m.name || m.redirect
            );
          },
          immediate: true, // 这⼀⾏要加上，让它⼀开始执⾏⼀次
        },
      },
```

## vue-router源码实现

### 通常用法

```
// krouter.js
import Home from "./views/Home";
import About from "./views/About";
import VueRouter from "./kvue-router";
Vue.use(VueRouter);
export default new VueRouter({
  routes: [
    { path: "/", component: Home },
    { path: "/about", component: About },
  ],
});
// main.js
import router from "./krouter";

```

### 具体实现

超级简陋的实现

核心是有个 current 保存当前 path，只要路由一变化，相应的组件就重新刷新，利用的也是 vue 响应式，所以写的东西和 vue 也是紧耦合的

```js
// kvue-router.js
let Vue;
class VueRouter {
  constructor(options) {
    this.$options = options;
    this.routeMap = {};
    this.app = new Vue({
      data: {
        current: "/",
      },
    });
  }
  // 绑定事件
  init() {
    this.bindEvents();
    this.createRouteMap(this.$options);
    this.initComponent();
  }
  bindEvents() {
    window.addEventListener("load", this.onHashChange.bind(this), false);
    window.addEventListener("hashchange", this.onHashChange.bind(this), false);
  }
  // 路由映射表
  createRouteMap(options) {
    options.routes.forEach((item) => {
      this.routeMap[item.path] = item;
    });
  }
  initComponent() {
    Vue.component("router-link", {
      props: {
        to: String,
      },
      render(h) {
        return <a href={this.to}>{this.$slots.default}</a>;
        // return h(
        //   "a",
        //   {
        //     attrs: {
        //       href: "#" + this.to,
        //     },
        //   },
        //   [this.$slots.default]
        // );
      },
    });
    Vue.component("router-view", {
      render: (h) => {
        var component = this.routeMap[this.app.current].component;
        return h(component);
      },
    });
  }
  // 设置当前路径
  onHashChange() {
    this.app.current = window.location.hash.slice(1) || "/";
  }
}
// 插件逻辑
VueRouter.install = function (_Vue) {
  Vue = _Vue;

  Vue.mixin({
    beforeCreate() {
      if (this.$options.router) {
        // 确保是根组件时执⾏⼀次，将router实例放到Vue原型，以后所有组件实例就均有$router
        Vue.prototype.$router = this.$options.router;
        this.$options.router.init();
      }
    },
  });
};

```

> 测试注意： 
>
> 不要出现router-view嵌套，因为没有考虑，把Home中的router-view先禁⽤ 
>
> 导航链接修改为hash

## render函数详解

⼀些场景中需要 JavaScript 的完全编程的能⼒，这时可以⽤渲染函数，它⽐模板更接近编译器。

```js
 render(h) { return h(tagName, {...props}, [children]) }
```

### createElement函数

```js
{
  // 与 `v-bind:class` 的 API 相同，
  // 接受⼀个字符串、对象或字符串和对象组成的数组
  class: {
    foo: true,
    bar: false,
  },
  // 与 `v-bind:style` 的 API 相同，
  // 接受⼀个字符串、对象，或对象组成的数组
  style: {
    color: "red",
    fontSize: "14px",
  },
  // 普通的 HTML 特性
  attrs: {
    id: "foo",
  },
  // 组件 prop
  props: {
    myProp: "bar",
  },
  // DOM 属性
  domProps: {
    innerHTML: "baz",
  },
  // 事件监听器在 `on` 属性内，
  // 但不再⽀持如 `v-on:keyup.enter` 这样的修饰器。
  // 需要在处理函数中⼿动检查 keyCode。
  on: {
    click: this.clickHandler,
  },
};
```

### 例子

```js
// <div id="box" class="foo"><span>aaa</span></div>
Vue.component("comp", {
  // template:'<div id="box" class="foo"><span>aaa</span></div>', 不能在这里写 template
  render(h) {
    // 子元素也要调用 h 函数
    return h("div", { class: { foo: true }, attrs: { id: "box" } }, [
      h("span", "aaa"),
    ]);
  },
  // jsx 写法是可以的，但这也只是上面的语法糖
  // render(h) {
  //   return <div id="box" class="foo"><span>aaa</span></div>
  // }
});
```

## 拓展 

### vue插件

```js
// 插件定义
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局⽅法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  };
  // 2. 添加全局资源
  Vue.directive("my-directive", {
    bind(el, binding, vnode, oldVnode) {
      // 逻辑...
    },
    //...
  });
  // 3. 注⼊组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    },
    //...
  });
  // 4. 添加实例⽅法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  };
};
// 插件使⽤
Vue.use(MyPlugin);

```

范例：移动$bus到插件

```js
class Bus {
    constructor(){}
    emit(){}
    on(){}
}

Bus.install = function (Vue) {
    Vue.prototype.$bus = new Bus();
}

export default Bus
```

### 组件混⼊：mixin 

混⼊ (mixin) 提供了⼀种分发 Vue 组件中可复⽤功能的灵活⽅式

```js
// 定义⼀个混⼊对象
var myMixin = {
  created: function () {
    this.hello();
  },
  methods: {
    hello: function () {
      console.log("hello from mixin!");
    },
  },
};
// 定义⼀个使⽤混⼊对象的组件
var Component = Vue.extend({
  mixins: [myMixin],
});

```

范例：移动dispatch和broadcast到mixins

```js
// 混入其实就是将一个对象添加到别的组件里去
export default {
  methods: {
    dispatch(eventName, data) {
      let parent = this.$parent;
      // 查找父元素
      while (parent) {
        // 父元素用$emit触发
        parent.$emit(eventName, data);
        // 递归查找父元素
        parent = parent.$parent;
      }
    },
    boardcast(eventName, data) {
      boardcast.call(this, eventName, data);
    },
  },
};

function boardcast(eventName, data) {
  this.$children.forEach((child) => {
    // 子元素触发$emit
    child.$emit(eventName, data);
    if (child.$children.length) {
      // 递归调用，通过call修改this指向 child
      boardcast.call(child, eventName, data);
    }
  });
}

```

## router-view嵌套的问题

[vue-router 源码](https://github.com/vuejs/vue-router/blob/dev/src/components/view.js)