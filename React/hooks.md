入门：https://codesandbox.io/s/react-hooks-demo1-cqdzt

dan 的完整指南：https://overreacted.io/zh-hans/a-complete-guide-to-useeffect

useState 不能存在于条件语句中，hooks 的状态记录是根据顺序去记录的，所以如果在条件判断的语句中去使用 useState 声明变量是不对的，这是 hooks 特殊的限制

用 useState 得到的 `[state, setState]` 的 `setState` 也接受一个函数，函数的参数就是 state 的旧值，函数返回的值就会设置成新的值

`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`