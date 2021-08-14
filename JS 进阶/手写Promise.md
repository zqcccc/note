# 手写 Promise

1. Promise 是一个类，构造参数是一个执行器函数，会立即执行
2. Promise 有三种状态，分别是成功 fulfilled，失败 rejected，等待 pending，状态一旦确定就不可更改
3. 执行器函数参数 resolve 和 reject，是两个函数，resolve 让状态变成 fulfilled，reject 让状态变成 rejected
4. then 方法内部就是判断状态是什么，成功就调用成功回调，失败就调用失败回调，then 被定义在原型对象中
5. then 里面两个回调函数都有参数，成功就是成功返回的结果，是失败返回的原因
6. 执行器里如果写的代码是异步代码，then 里面写的回调就先保存起来，然后等 resolve 或者是 reject 的时候再调用回调。
7. 一个 promise 是可以被多次 then 的，这样的话，回调就要用数组去存储（这里的多次 then 并不是链式调用）
8. then 是可以被链式调用的，then 方法会返回一个新的 promise
   - then 的回调返回值需要判断是普通值还是 promise 对象
   - 如果是普通值，直接 resolve
   - 如果是 promise 要返回 promise 对象返回的值
   - 在根据 promise 对象返回的结果决定 resolve 还是 reject（用then方法）

9. then 里不允许返回自己，这会造成循环调用的错误
10. Promise 里的构造函数和 then 里面都要捕获错误，如果有错误就 reject
11. then 方法的参数都是可选参数
12. 静态 all 方法
13. 静态 resolve 方法
14. finally 方法，无论 promise 的状态是成功还是失败都要调用这里面的回调方法
    - finally 返回一个新 promise，也可以用 then 继续链式调用
    - finally 里的回调如果是异步的要先执行了异步再执行后面的 then
    - 用 `Promise.resolve` 和 `then` 就能实现

15. catch 方法，用 `then` 可以不传递第一个回调即成功回调，只传失败的回调来实现

