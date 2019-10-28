# 对 Props 进行类型检查

## 为什么需要类型检查？

JavaScript 是一门弱类型的语言，允许变量类型做隐式转换。也正是因为这个特性，JavaScript 中有很多错误都是类型错误导致的。为了减少这种错误，我们可以在 React 中引入类型检查模块。

## React 中的类型检查：prop-types 包

### 例子

- 导入包

  ```
    import PropTypes from 'prop-types';
  ```

- 编写组件

  ```
    class Greeting extends React.Component {
        render() {
            return <h1>Hello, {this.props.name}</h1>
        }
    }
  ```

- 新增类型检查

  ```
    Greeting.propTypes = {
        name: PropTypes.string
    }
  ```

- 检验类型检查

  我们把 Greeting 组件的属性 name 设置成 String 类型，也就是说父组件通过该属性传递数据的时候，需要传递 String 类型的数据，否则会显示类型错误的警告。

  我们可以尝试传递一个数字类型的属性：

  ```
    <div>
        <Greeting name={123} />
    </div>
  ```

  可以看到页面中会出现类型错误的告警：

  ![图片](https://p.qpic.cn/qqconadmin/0/84361fe0ac4f451fb46666a877915451/0)

PropTypes 包含一整套验证器，可用于确保你接收的数据是有效的。在上面的示例中，我们使用了 PropTypes.string。
出于性能原因，propTypes 只在开发模式下进行检查。

## prop-types 的多种验证器

```
MyComponent.propTypes = {
    // 属性可以声明为 JS 原生类型
    optionalArray: PropTypes.array,
    optionalBool: PropTypes.bool,
    optionalFunc: PropTypes.func,
    optionalNumber: PropTypes.number,
    optionalObject: PropTypes.object,
    optionalString: PropTypes.string,
    optionalSymbol: PropTypes.symbol,

    /* 任何东西都可以被渲染:numbers, strings, elements,或者是包含这些类型的数组(或者是片段)。*/
    optionalNode: PropTypes.node,

    // 一个 React 元素。
    optionalElement: PropTypes.element,

    // 属性也可以声明为类的一个实例。
    // 使用 JS 的 instanceof 运算符。
    optionalMessage: PropTypes.instanceOf(Message),

    // 属性声明为特定的值，类似于枚举
    optionalEnum: PropTypes.oneOf(['News', 'Photos']),

    // 一个对象可以是多种类型其中之一
    optionalUnion: PropTypes.oneOfType([
        PropTypes.string,
        PropTypes.number,
        PropTypes.instanceOf(Message)
    ]),

    // 一个某种类型的数组
    optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

    // 属性值为某种类型的对象
    optionalObjectOf: PropTypes.objectOf(PropTypes.number),

    // 一个特定形式的对象
    optionalObjectWithShape: PropTypes.shape({
        color: PropTypes.string,
        fontSize: PropTypes.number
    }),

    // 使用 'isRequired' 链接上述的类型设定，可以确保在没有提供 Props 的情况下显示警告。
    requiredFunc: PropTypes.func.isRequired,

    // 任何数据类型的值
    requiredAny: PropTypes.any.isRequired,

    /* 属性也可以声明为自定义的验证器。验证失败则返回 Error 对象。不要使用 `console.warn` 或者 throw ，*/
    // 因为这不会在 `oneOfType` 类型的验证器中起作用。
    customProp: function (props, propName, componentName) {
        if (!/matchme/.test(props[propName])) {
            return new Error(
                'Invalid prop `' + propName + '` supplied to' +
                ' `' + componentName + '`. Validation failed.'
            );
        }
    },

    // 属性可以声明`arrayOf`和`objectOf`类型的验证器，如果验证失败，则需要返回Error对象。
    // 可以在数组或者对象的每一个元素上调用验证器。验证器的前两个参数分别是数组或者对象本身和当前元素的键值。
    customArrayProp: PropTypes.arrayOf(function (propValue, key, componentName, location, propFullName) {
        if (!/matchme/.test(propValue[key])) {
            return new Error(
                'Invalid prop `' + propFullName + '` supplied to' +
                ' `' + componentName + '`. Validation failed.'
            );
        }
    })
};
```

## 属性默认值

你可以通过配置 defaultProps 为 Props 定义默认值：

```
// 为属性指定默认值:
Greeting.defaultProps = {
    name: 'Stranger'
};
```

defaultProps 用来确保 this.props.name 在父组件没有特别指定的情况下，有一个初始值。类型检查发生在 defaultProps 赋值之后，所以类型检查也会应用在 defaultProps 上面，我们也需要保证所设置的默认值符合类型检查设定的类型。

## 使用 PropTypes.element 限制单个子代

使用 PropTypes.element，可以指定只传递一个子代

### 例子

```
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
```

this.props.children 是父组件在 MyComponent 中添加的子节点，当 children 包含多个兄弟节点，而不是只有一个节点时，会打印错误告警。

错误：

```
<div>
    <MyComponent>
        <div>hello</div>
        <div>props</div>
    </MyComponent>           
</div> 
```

正确：

```
<div>
    <MyComponent>
        <div>hello props</div>
    </MyComponent>           
</div> 
```