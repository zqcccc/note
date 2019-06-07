# 断言为 any 类型

看到标题可能很多同学会有疑问，为什么要断言为 any 类型？之前不是说要尽量让类型明确吗。

其实这里并不冲突，为了让代码清晰便于阅读，在使用 TypeScript 开发的过程中我们一定要尽量避免声明 any 类型。但是在实际的开发过程中，难免遇到某些场景使得 TypeScript 无法编译通过，这里例举以下两个场景。

## 方法被调用的时候，实际传入的参数有可能是一个更加具体的值

我们来看如下代码：

```ts
function handler (e: Event) {
    let touchEvent = e as TouchEvent;
}
```

上方代码可以通过编译，但是如下代码却会报错

```ts
function handler (e: Event) {
    let $element = e as HTMLElement; // 类型“Event”不可转换为类型“HTMLElement”。类型“Event”中缺少属性“accessKey”。
}
```

这里我们期望的 $element 是一个 DOM 元素，所以 TS 会尝试去访问 e 是否存在 DOM 元素所必须有的属性，第一个访问的就是 accessKey 属性，因为这里的 e 是 Event 类型，发现访问不到，就会提示错误。

这时如果我们需要强行声明，我们可能就需要 **将值断言为 any**，类似如下代码：

```ts
function handler (e: Event) {
    let $element = e as any as HTMLElement; // 双重断言
}
```

这里也可以理解为进行了双重断言，最终通过编译。

## 引入第三方库，造成无法通过编译

假设一个场景，我们引入了一个较老版本的 html2canvas 库，其在 windows 对象上绑定了 html2canvas 函数，我们的用法可能是如下代码：

```ts
import './html2canvas';

let $element = document.body;

window.html2canvas($element, canvas => {
    console.log(canvas.toDataURL());
}); // 类型“Window”上不存在属性“html2canvas”
```

这时候我们可以将 window 断言为 any 类型来避免编译不通过

```ts
import './html2canvas';

let $element = document.body;

(window as any).html2canvas($element, canvas=>{
    console.log(canvas.toDataURL());
});
```