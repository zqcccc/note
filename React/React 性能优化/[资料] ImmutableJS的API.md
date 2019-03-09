# ImmutableJS 常用 API

List 和 Map 是我们在 Immutable 中用的最多的两个数据类型，对应原生 JS 中的数组和对象，我们这里主要讲这两个类型相关的 Api。

### fromJS(jsValue, reviver)

将普通 JS 对象和数组转换为 Immutable 中的 Map 和 List。jsValue 代表传入的 JS 代码，reviver 是一个选填参数，默认会将 JS 的数组和对象分别转成 List 和 Map 类型。

```
fromJS([1, 2]); // 等于 List([1, 2])
fromJS({ a: 1 }); // 等于 Map({ a: 1 })
```

### toJS()

将 Immutable 类型的数据转成 JS 类型的数据:

```
Immutable.List([1, 2, 3]).toJs(); // [1, 2, 3]
Immutable.Map({ name: 'Mike' }).toJs(); // { name: 'Mike' }
```

### 数据创建： List()、Map()

创建一个 List/Map 类型的对象：

```
Immutable.List();
Immutable.List([1, 2]);

Immutable.Map();
Immutable.Map({ name: 'Mike' });
```

### size

获取 List/Map 的长度：

```
Immutable.List([1, 2]).size; // 2
Immutable.Map({ name: 'Mike', gender: 'male' }).size; // 2
```

### 类型判断：List.isList()、Map.isMap()

判断是否是 List/Map 类型的数据：

```
List.isList([]); // false
List.isList(List[]); // true

Map.isMap({}); // false
Map.isMap(Map()); // true
```

### 获取属性：get(key) 和 getIn(keyArr)

获取数据结构中的数据。key 代表键值，keyArr 则代表键值的路径。

```
Immutable.List([1, 2]).get(0); // 1
Immutable.Map({ name: 'Mike' }).get('name'); // Mike

Immutable.List([1, [2, 3]]).getIn([1, 1]); // 3
Immutable.Map({ userInfo: { name: 'Mike', gender: 'male' } }).getIn(['userInfo', 'gender']); // male
```

### 设置属性：set(key, value) 和 setIn(keyArray, value)

返回原 List 的副本，且新 List 的 key 键对应的值被设置为 value。
当数据类型为 List 时, key 为 数字，此时当 key 为负数则从尾部开始索引。

```
// Map 类型
Immutable.Map({ name: 'Mike' }).set('name', 'Nick'); // Immutable.Map({ name: 'Nick' })

// List 类型
// 索引 index 为正
Immutable.List([1, 2, 3]).set(1, '2'); // Immutable.List([1, '2', 3])
// 索引 index 为负，从尾部索引
Immutable.List([1, 2, 3]).set(-1, '2'); // Immutable.List([1, 2, '2'])

// setIn
Immutable.List([1, [2, 3]]).setIn([1, 1], 4); // Immutable.List([1, [4, 3]])
Immutable.Map({ userInfo: { name: 'Mike', gender: 'male' } }).getIn(['setIn', 'gender'], 'female')
```

### 移除属性：remove(key) 和 removeIn(keyArray)

该方法返回一个新的数据集合副本，且该副本的对应键值已被删除。

ALIAS: delete/deleteIn()：

```
Immutable.List([1, 2]).remove(1); // Immutable.List([1])
Immutable.Map({ name: 'Mike', gender: 'male' }).remove('name'); // Immutable.Map({ gender: 'male' })

Immutable.List([1, [2, 3]).removeIn([1, 1]); // Immutable.List([1, [2]])
Immutable.Map({ userInfo: { name: 'Mike', gender: 'male' } }).removeIn(['userInfo', 'name']); // Immutable.Map({ userInfo: { gender: 'male' } })
```

### 数据更新：update(key, updater) 和 updateIn(keyArray, updater)

该方法和 set 方法类似，不同的是 update 第二个参数 updater 是一个函数，而该函数的返回值将被设置为 key 键对应的值。List 和 Map 类型都可使用此方法。

```
const defaultMap = Immutable.Map({ userInfo: { name: 'Mike' } });
const map1 = defaultMap.update('userInfo', info => { name: 'Nick' });
const map2 = defaultMap.updateIn(['userInfo', 'name'], name => 'Nick');
```

### 属性存在判断：has(key) 和 hasIn(keyArray)

判断数据中是否存在某一个 key：

```
Immutable.List([1, 2]).has(2); // false
Immutable.Map({ name: 'Mike' }).has('name'); // true

Immutable.List([1, [2, 3]).hasIn([1, 2]); // false
Immutable.Map({ userInfo: { name: 'Mike', gender: 'male' } }).hasIn(['userInfo', 'name']); // true
```

### List.push()、List.pop()、List.unshift() 和 List.shift()

Immutable List 的这四个方法和 JS Array 对应的方法是类似的，区别在于，JS 的方法会改变原数组，且返回的是改变后的数组的长度，而 Immutable List 不会改变原 List，且会返回的是处理好的新 List。

```
List([1, 2]).push(3); // List([1, 2, 3])
List([1, 2]).pop(); // List([1])
List([1, 2]).unshift(0); // List([0, 1, 2])
List([1, 2]).shift(); // List([2])
```

### 插入：List.insert(index, value)

该方法返回 List 的副本，副本中 index 的索引处被插入 value 值，索引处后面的值往右偏移1位。

```
List([ 0, 1, 2, 3, 4 ]).insert(2, 5); // List [ 0, 1, 5, 2, 3, 4 ]
```

### 数据拼接：concat(List)

用法等同 JS 中的 concat，结果返回一个新的 List 对象。

### 数据遍历：map()

遍历 List/Map 对象，最后返回一个新的对象：

```
List([1, 2, 3]).map(val => val * 2); // List([ 2, 4, 6])
Map({ a: 1, b: 2}).map(val => val * 2); // Map({ a: 2, b: 4})
```



这篇文档只是节选了 ImmutableJs 中比较常用的 Api，还有更多的方法可点击 [官方文档](https://facebook.github.io/immutable-js/docs/#/) 进行查看。