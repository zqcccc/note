# PureComponent组件中的浅比较

## 浅比较函数：shallowEqual

以下是 React 的 shouldComponentUpdate 函数源码的部分实现：

```js
else if (type.prototype && type.prototype.isPureReactComponent) {
  shouldUpdate = !shallowEqual(oldProps, props) || !shallowEqual(oldState, state);
}
```

如上，当组件为 PureComponent 类型时，React 会使用 shallowEqual 函数对属性和状态进行一层浅比较。

我们来看看这个 shallowEqual 函数的源码实现：

```js
const hasOwnProperty = Object.prototype.hasOwnProperty;

function is(x, y) {
  if (x === y) {
    return x !== 0 || y !== 0 || 1 / x === 1 / y;
  } else {
    return x !== x && y !== y;
  }
}

function shallowEqual(objA: mixed, objB: mixed): boolean {
  if (is(objA, objB)) {
    return true;
  }

  if (
    typeof objA !== 'object' ||
    objA === null ||
    typeof objB !== 'object' ||
    objB === null
  ) {
    return false;
  }

  const keysA = Object.keys(objA);
  const keysB = Object.keys(objB);

  if (keysA.length !== keysB.length) {
    return false;
  }

  for (let i = 0; i < keysA.length; i++) {
    if (
      !hasOwnProperty.call(objB, keysA[i]) ||
      !is(objA[keysA[i]], objB[keysA[i]])
    ) {
      return false;
    }
  }

  return true;
}

export default shallowEqual;
```

shallowEqual 函数包含了 4 次的比较：

- 使用 is 函数进行一次升级版的严格比较。
- objA 和 objB 其中有一个不是对象或者值为 null，返回 false。
- objA 和 objB 的 key 长度不一致，返回 false。
- 遍历 objA 第一层的属性，如果 objB 没有对应的属性或者属性值无法与 objA 对应的属性值通过 is 函数的严格比较，则返回 false。

需要注意，第一次比较中的 is 函数其实就是 Object.is() 的一个实现，它和全等操作符 === 的区别在于，后者认为：

- 数字值 -0 和 +0 是相等
- NaN 不等于 NaN

而 is 函数在这两种情况下的结果恰是相反，显然其更为符合我们的预期。

### 浅比较中的嵌套数据

shallowEqual 存在的问题在于，当 key 对应的值是一个对象时，根据 is 函数判断其值是否有改变是不够的，因为该 key 对应的对象改变但是对象的引用地址未改变时，is 函数会将其视为相等。所以这就是为什么要求我们在 PureComponent 组件内更新一个类型为对象的组件状态时需要使用一个新的对象进行替代。这也算是浅比较对于嵌套数据不友好的一个地方，但对我们来说并不算是一个问题。比较嵌套数据十分耗费性能，我们完全可以通过传递一个新的对象解决该问题，且不造成额外的性能花销。