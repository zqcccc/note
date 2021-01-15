组件仍然可以使用以前的方式定义(到处组件选项对象，或者使用 `Vue.extend()`

导出的是一个普通对象时，TS无法推断出对应的类型

VSCode 可以推断出类型成员是因为使用了 Vue 插件

必须使用 `vue.extend()` 方法确保TS能够有正常的类型判断

axios 请求拦截器里一般是帮我们去添加一些 Headers，比如 token，



Token 过期的话，有两种方式去处理

1. 客户端判断 token 是否过期过期去刷新 token 再发起请求
   - 客户端去比较时间是不靠谱的，时间可以被篡改
   - 需要在请求的时候拦截
2. 直接请求，服务端返回过期的响应后再刷新 token 再去请求
   - 多请求一次，但是比较靠谱，没有问题
   - 需要在响应的时候拦截

处理上面的过期问题在 axios 里也是用响应拦截器去处理，这里细节也是比较多的

加入收到响应码是 401 需要新建一个 axios 实例去请求刷新 token，因为如果本身状态就没有 token，那还用原来的 axios 实例的话就会又收到 401，又被响应拦截，然后就无限循环，这样显然是很有问题的

发起刷新 token 请求成功后，需要把状态再次放到 vuex 中，然后在用原来老的 axios 实例的配置去请求之前失败的接口，这样流程就是完整的

上面处理 401 的情况也不是完美的，如果以过期的 token 同时发起了多个请求，那么就会出现多次同时刷新 token 的情况，但其实通常来说，刷新 token 的请求也是需要带上 token 的，这个 token 用来刷新的话只能用一次，这样其实只刷新成功了一次，刷新 token 失败的话，对应发的请求也是要出错的

需要在最外部，去定义一个布尔值判断是否在发起刷新 token 的状态，还要一个数组去接收在刷新 token 的状态时发起的请求，后面如果刷新 token 的请求成功后，需要把数组遍历执行后再清空数组

```js
let isRfreshing = false // 控制刷新 token 的状态
let requests: Function[] = [] // 存储刷新 token 期间过来的 401 请求

// 以下是在响应拦截器的错误处理中
// request 就是一个 axios 实例
// 刷新 token 代码片段
if (!isRfreshing) {
  isRfreshing = true // 开启刷新状态
  // 尝试刷新获取新的 token
  return refreshToken().then(res => {
    if (!res.data.success) {
      throw new Error('刷新 Token 失败')
    }

    // 刷新 token 成功了
    store.commit('setUser', res.data.content)
    // 把 requests 队列中的请求重新发出去
    requests.forEach(cb => cb())
    // 重置 requests 数组
    requests = []
    return request(error.config)
  }).catch(err => {
    console.log(err)
    Message.warning('登录已过期，请重新登录')
    store.commit('setUser', null)
    redirectLogin()
    return Promise.reject(error)
  }).finally(() => {
    isRfreshing = false // 重置刷新状态
  })
}

// 刷新状态下，把请求挂起放到 requests 数组中
return new Promise(resolve => {
  requests.push(() => {
    resolve(request(error.config))
  })
})
```

