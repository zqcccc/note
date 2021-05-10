# TS 在项目中的实战

> 免责声明：全面而肤浅，深刻而片面，以下出现肤浅或者片面的地方请见谅

## 背景

1. 部分页面维护周期可能很长，别人负责老的页面在你接手的时候可能一脸懵
2. 太多第三方库已经支持 ts 了，vscode 里跳转到定义都是优先跳到类型文件（VSCode 官方觉得这是个 feature），这时如果你看不懂 TS 类型的话，你还是会一脸懵

## 基础

学习怎么在项目中应用之前呢，还是要先巩固一下 TS 的基础内容哈

TS 在 VSCode 中的支持非常好，所以文中的代码在 VSCode 中写一遍，然后把鼠标移到变量上直接看到类型的效果会更好

TS 作为 JS 的超集，每个 JS 开发者都能很快上手，你可以把写 TS 理解成在写有显式标注类型的 JS

```ts
/**
 * 原始类型 Primitive type
 */

const a: string = ''

const b: number = 100

const c: boolean = true

const d: null = null

const e: undefined = undefined

const f: void = undefined

/**
 * 非原始类型
 */
let g: object = {}
let g: object = []
let g: object = () => {}

/**
 * 数组类型
 */
const arr1: Array<number> = [1, 2, 3]
const arr2: number[] = [1, 2, 3]

function oldSum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0)
}
oldSum(1, 2, 'str')
function sum(...args: number[]) {
  return args.reduce((pre, cur) => pre + cur, 0)
}
sum(1, 2, 'str')

/**
 * 元组类型 tuple
 * 是长度固定，每一项的类型都确定的数组
 * React.useState
 */
const tuple: [number, string] = [18, 'zqc']

const [age, name] = tuple

const arr = Object.entries({
  age: 18,
  height: 100
})

/**
 * 枚举 Enum
 * 注意里面是等号 =
 */
enum AnchorType {
  freeAnchor = 1, // 自由主播
  laborAnchor = 2, // 工会主播
  ourAnchor = 3, // 直签主播
}
const anchor = {
  name: 'zqc',
  type: AnchorType.freeAnchor // 使用方式跟对象一样
}

/**
 * 函数类型
 */
// 函数声明
function func1 (a: number, b: number): string {
  return ''
}
func1(123)

// 函数表达式
const func2: (a: number, b: number) => string = function(a: number, b: number): string {
  return 'func2'
}
func2(123)

/**
 * 任意类型
 */
function stringify(value: any) {
  return JSON.stringify(value)
}
stringify('string')
stringify(100)
// ts 不会对 any 类型进行检查，所以不会报语法错误，注意只是 ts 不报语法错误，但是兼容老代码的时候是难免会用到 any

/**
 * 隐式类型推断
 */
let age = 18
age = 'string'
let foo
foo = 199
// 还是建议为每个变量明确类型

/**
 * 类型断言
 */
let res

const data1 = res as number // `as` 操作符在 `.ts` 文件和 `.tsx` 文件里都可用

const data2 = <number>res // jsx 下不能使用，所以在项目中我们应该用的也不多

// 类型断言 并不是 类型转换，这里并不是把一个类型转换成了另一类型，因为类型转换是代码在运行时的一个概念，这里的类型断言只是在编译过程的一个概念，当代码编译成 js 后这个断言就不会存在了
```

## 接口

接口和类型是我们使用 TS 必须掌握的概念，大部分情况下我们都是用来定义一个复杂的数据对象

```tsx
/**
 * 接口
 */
interface Pages {
  currentPage: number;
  pageSize?: number;
}

function getDataByPage(pages: Pages) {
  console.log(pages.currentPage)
  console.log(pages.pageSize)
  return pages
}
getDataByPage({ currentPage: 1 })

// prop 只是别名，取什么都可以
interface Cache {
  [prop: string]: number;
}
const cache: Cache = {}
cache.foo = 1
cache.bar = 2

/**
 * 类型 type
 * 跟接口差不多，一般我们用的比较多的也是这个
 */
type Example = {
  name: string;
  age: number;
}
```



## 泛型

如果你只接触过 js，泛型算是比较陌生的概念，但其实不难哈

```typescript
// 创建一个数字数组的函数定义
function createNumberArray (length: number, value: number): number[] {
  const arr = Array<number>(length).fill(value)
  return arr
}
// 创建一个字符串数组的函数定义
function createStringArray (length: number, value: string): string[] {
  const arr = Array<string>(length).fill(value)
  return arr
}
createNumberArray(3, 10)
createStringArray(4, '')
// 上面的函数定义其实可以看出来很冗余，可以抽象成下面的泛型写法

/**
 * 泛型
 * 就是指我们在定义函数，接口，类型的时候，没有指定具体的类型
 * 而是在调用的时候才指定具体类型的一种特征
 * 为的是极大程度的复用代码
 */
function createArray<T> (length: number, value: T): T[] {
  // 在 ts 中的写法就是上面这种，一般都是一对中括号，里面就是你传入的模板，这个模板是可以是多个的，名字也不是非得是 `T`，这里 `T` 也就是形参，取什么名字都行哈
  const arr = Array<T>(length).fill(value)
  return arr
}
createArray<number>(3, 10)
createArray<string>(4, '')
```

对我们来说，大部分情况都不需要我们去定义泛型，我们只要像调用函数一样去调用就行了，学会用就足够了，比如说，你要定义一个数组类型，数组里的每个元素是什么东西你得定义一下，不然 ts 怎么知道你数组里都是存的些什么妖魔鬼怪

学会了泛型，其实你就具备了开发 TS 的能力了，只差临门一脚了，相信自己一定行💪

因为其实很多流行的三方库都是有对应的 `@types/xxx` 类型库的，这些类型库都把我们用到的库的类型都定义好了，我们直接使用就有类型约束和类型提示了。

比传说 `@types/react` 这个库已经帮我们把 react 的很多内置类型都封装好了，我们学会使用就够了，不光是 react 这个库哈，，方便我在写 TS 代码的时候直接使用。

对应到项目中就是，有一个例子可以帮助我们很好地理解：在 React 中除了傻瓜 UI 组件，所有的组件都有 props 和 state 对象，react 已经定义好一个 react 组件的类型是怎样的了，我们只要传入泛型的类型就行了

![image-20210417225906227](../media/image-20210417225906227.png)

在 `react` 中写一个 `input` 标签的 `onChange` 事件方法就是

```tsx
const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  	// 也是通过尖括号将类型传入 React.ChangeEvent 中，然后就定义了 e 的类型
  	// HTMLInputElement 就是这个方法要挂载到的元素类型上
    setValue(e.target.value)
}
...
return <input onChange={onChange}/>
```

```tsx
// @types
interface ChangeEvent<T = Element> extends SyntheticEvent<T> {
    target: EventTarget & T;
}
```

当然了这里只是举例子说明泛型的用法，也不是所有的事件都要写这么详细哈，其实简单的 `onChange` 事件是不用的写类型的，直接写个行内函数我觉得也可以哈

```tsx
return <input onChange={e => setValue(e.target.value)}/>
```





## react 常用类型

```tsx

type AppProps = {
  message: string;
  count: number;
  disabled: boolean;
  /** array of a type! */
  names: string[];
  /** string literals to specify exact string values, with a union type to join them together */
  status: 'waiting' | 'success';
  /** any object as long as you dont use its properties (NOT COMMON but useful as placeholder) */
  obj: object;
  obj2: {}; // almost the same as `object`, exactly the same as `Object`
  /** an object with any number of properties (PREFERRED) */
  obj3: {
    id: string;
    title: string;
  };
  /** array of objects! (common) */
  objArr: {
    id: string;
    title: string;
  }[];
  /** a dict object with any number of properties of the same type */
  dict1: {
    [key: string]: MyTypeHere;
  };
  dict2: Record<string, MyTypeHere>; // equivalent to dict1
  /** any function as long as you don't invoke it (not recommended) */
  onSomething: Function;
  /** function that doesn't take or return anything (VERY COMMON) */
  onClick: () => void;
  /** function with named prop (VERY COMMON) */
  onChange: (id: number) => void;
  /** alternative function type syntax that takes an event (VERY COMMON) */
  onClick(event: React.MouseEvent<HTMLButtonElement>): void;
  /** an optional prop (VERY COMMON!) */
  optional?: OptionalType;
}
  
export declare interface AppProps {
  children1: JSX.Element; // bad, doesnt account for arrays
  children2: JSX.Element | JSX.Element[]; // meh, doesn't accept strings
  children3: React.ReactChildren; // despite the name, not at all an appropriate type; it is a utility
  children4: React.ReactChild[]; // better, accepts array children
  children: React.ReactNode; // best, accepts everything (see edge case below)
  functionChildren: (name: string) => React.ReactNode; // recommended function as a child render prop type
  style?: React.CSSProperties; // to pass through style props
  onChange?: React.FormEventHandler<HTMLInputElement>; // form events! the generic parameter is the type of event.target
  //  more info: https://react-typescript-cheatsheet.netlify.app/docs/advanced/patterns_by_usecase/#wrappingmirroring
  props: Props & React.ComponentPropsWithoutRef<"button">; // to impersonate all the props of a button element and explicitly not forwarding its ref
  props2: Props & React.ComponentPropsWithRef<MyButtonWithForwardRef>; // to impersonate all the props of MyButtonForwardedRef and explicitly forwarding its ref
}
```

## 常见问题 Argument of type 'xxx' is not assignable to parameter of type 'xxx'. 

这个应该可能是你写 ts 遇到的最多的报错之一了，主要原因一般是某个函数需要的值和你传入的值类型不一致导致的。

很多情况是类型可能是联合类型，带了些不符合要求的类型，这种联合类型可能是不能直接赋值的，但也不能把空值给去掉，因为我们的数据是存在有空值的情况的，这个时候就可以用条件判断的语句让数据类型确定，这里也体现了 TS 是如何在编码阶段就干掉了一些错误

```tsx
function Test() {
  const swiperDom = useRef<HTMLDivElement>(null) // 这个 ref 就是为了拿真实 DOM 节点
  
  // new Swiper(swiperDom.current, {}) // 这就报出上面的问题，实例化 Swiper 的时候，入参只能是真实 DOM 或者 css选择器string
  
  useEffect(() => {
    if(swiperDom.current !== null) {
      // 在这里面去实例化就是不会报错的
      new Swiper(swiperDom.current, {})
    }
  })
  
  return <div ref={swiperDom}></div>
}
```

或者使用 as 类型断言也是可以解决这个问题的，但你要确保你的类型断言是一定成立的

## Custom Hooks

这里主要讲元组可以用 `as const` 去定义

```tsx
export function useLoading() {
  const [isLoading, setState] = React.useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  return [isLoading, load] as const; // infers [boolean, typeof load] instead of (boolean | typeof load)[]
}
```

```tsx
export function useLoading() {
  const [isLoading, setState] = React.useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  return [isLoading, load] as [
    boolean,
    (aPromise: Promise<any>) => Promise<any>
  ];
}
```



## 不建议的写法

- class 的 render 方法和 hooks 返回的除了 jsx 和 null，不应该返回其他类型
- 刚开始过渡的时候，可以先用 any 来避免报错，但是后面还是推荐把类型都定义出来
- 不建议代码全都是 any，这样就成了 anyscript 而不是 typescript 了

## 建议的写法

class 的 state 如果能定义类型的话，在访问 this.state 和 this.setState 会有更多的提示，

```tsx
type MyState = {}
class App extends React.Component<{}, MyState> { state: MyState = {} }
```

在React和TypeScript项目中编写props时，要记住这些内容

- 使用ss符号`/** comment */`向props添加描述性注释。
- 无论你为组件props使用类型（type）还是接口（interface），都要始终如一地使用它们。

```tsx
import React from 'react'

type Props = {
  /** color to use for the background */
  color?: string;
  /** standard children prop: accepts any valid React Node */
  children: React.ReactNode;
  /** callback function passed to the onClick handler*/
  onClick: ()  => void;
}

const Button: React.FC<Props> = ({ children, color = 'tomato', onClick }) => {
   return <button style={{ backgroundColor: color }} onClick={onClick}>{children}</button>
}

```

如果你用的 hooks 的话，那大部分情况是甚至不写类型都有更好的类型推论，所以还是比较建议都写 hooks

- 搜索 typescript model generator，会有很多 json 转 ts model 的网站，可以帮我我们生成类型声明的文件，比如 https://app.quicktype.io/
- 或者你可以用 vscode 里的拓展插件 [JSON to TS](https://marketplace.visualstudio.com/items?itemName=MariusAlchimavicius.json-to-ts)，不止这一款，可以尝试别的拓展，有好用的可以试着分享出来给大家一起用哈

## redux

mapDispatch

```tsx
// action
const addTodo = (content: string) => dispatch => {
  dispatch({
    type: 'ADD_TODO',
    payload: content
  })
}

// 组件中 mapDispatch
const mapDispatchToProps = (dispatch) => {
	return bindActionCreators({
    addTodo
  }, dispatch)
}

// 这就会根据action自动生成类型，如果有其他组件的传过来的属性直接和这个类型合并就行了
type TodoInputProps = ReturnType<typeof mapDispatchToProps>
```

mapState 的时候，如果有定义出 store 的根 state 的类型，那在组件拿 store 的 state 的时候就会有类型了

```tsx
type ReducerType = typeof reducers // 根 reducers
type RootState = {
  [key in keyof ReducerType]: ReturnType<ReducerType[key]>
}
// 在 reducer 中 action 其实是 any 类型，其实可以定义 reducer 的返回值类型来解决
const defaultState = {
  ...
}
function reducer(state, action): typeof defaultState {
  switch(action.type){
      
  }
}
```

## ts 版本问题

首先，假定你跟我一样经常用 vscode 开发的话，那么你可以在随便打开某个 ts 文件，然后在 vscode 的最下面的状态栏找到一个版本号，点击一下版本号就可以换编辑器用的 ts 版本号，希望你可以改成使用项目的版本号，而不是 vscode 默认的版本，换了后，vscode 也只是给你生成了 `.vscode` 文件夹，里面有 vscode 的配置

因为有的仓库是不会把 .vscode 的配置上传的，所以需要有上面这步

## 最后

没有必要觉得 ts 有好多新东西要学就畏惧哈，其实都很简单的，大部分情况下可以无脑地把文件后缀由 `.js` 变成 `.ts` 或 `tsx`，然后它报什么错就去解决就行了

要说有什么真的必须要做到的底线就是，不要提交任何有 ts 错误的代码，其实即使不写一个类型定义，ts 也不会有什么大问题，ts 会推论出你的数据类型，然后在你隐式转换类型的时候就给你报错，你不解决都不行，从编码阶段就把很多错误扼杀了这就是 ts 的优势

## 参考资料

平时很多文章都是讲的 ts 的知识，甚至是很基础的内容，作用可能不是很大，所以建议大家都看看下面的资料

[typescript-cheatsheets/react](https://github.com/typescript-cheatsheets/react) 这个讲的很多很细，建议看看

[typescript FAQs](https://github.com/microsoft/TypeScript/wiki/FAQ?)

[类型编程](https://juejin.cn/post/6885672896128090125#heading-12)

## 条件类型 Conditional Types（仅供参考）

这个部分不讲哈，感兴趣的同学可以自己下去搜索下哈😝

条件类型的语法实际上就是三元表达式：

```typescript
T extends U ? X : Y
```

> 如果你觉得这里的extends不太好理解，可以暂时简单理解为U中的属性在T中都有。

因此条件类型理解起来更直观，唯一需要有一定理解成本的就是 **何时条件类型系统会收集到足够的信息来确定类型**，也就是说，条件类型有可能不会被立刻完成判断。

在了解这一点前，我们先来看看条件类型常用的一个场景：**泛型约束**，实际上就是我们上面的例子：

```typescript
function pickSingleValue<T extends object, U extends keyof T>(obj: T, key: U): T[U] {
  return obj[key];
}
```

这里的`T extends object`与`U extends keyof T`都是泛型约束，分别**将T约束为对象类型**和将U约束为T键名的字面量联合类型。我们通常使用泛型约束来**“使得泛型收窄”**。

以一个使用条件类型作为函数返回值类型的例子：

```typescript
declare function strOrnum<T extends boolean>(
  x: T
): T extends true ? string : number;
```

在这种情况下，条件类型的推导就会被延迟（deferred），因为此时类型系统没有足够的信息来完成判断。

只有给出了所需信息（在这里是x值），才可以完成推导。

```typescript
const strReturnType = strOrNum(true);
const numReturnType = strOrNum(false);
```

同样的，就像三元表达式可以嵌套，条件类型也可以嵌套，如果你看过一些框架源码，也会发现其中存在着许多嵌套的条件类型，无他，条件类型可以将类型约束收拢到非常精确的范围内。

```typescript
type TypeName<T> = T extends string
  ? "string"
  : T extends number
  ? "number"
  : T extends boolean
  ? "boolean"
  : T extends undefined
  ? "undefined"
  : T extends Function
  ? "function"
  : "object";
```

## infer关键字（仅供参考）

`infer`是`inference`的缩写，通常的使用方式是`infer R`，`R`表示 **待推断的类型**。通常`infer`不会被直接使用，而是被放置在底层工具类型中，需要在条件类型中使用。看一个简单的例子，用于获取函数返回值类型的工具类型`ReturnType`:

```typescript
const foo = (): string => {
  return "linbudu";
};

// string
type FooReturnType = ReturnType<typeof foo>;
```

`infer`的使用思路可能不是那么好习惯，我们可以用前端开发中常见的一个例子类比，页面初始化时先显示占位交互，像Loading/骨架屏，在请求返回后再去渲染真实数据。`infer`也是这个思路，**类型系统在获得足够的信息后，就能将infer后跟随的类型参数推导出来**，最后返回这个推导结果。

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```

类似的，借着这个思路我们还可以获得函数入参类型、类的构造函数入参类型、Promise内部的类型等，这些工具类型我们会在后面讲到。

infer其实没有特别难消化的知识点，它需要的只是思路的转变，你要理解 **延迟推断** 的概念。