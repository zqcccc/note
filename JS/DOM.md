## DOM

Document Object Model 文档对象模型，它是针对 HTML 文档的 API (应用程序编程接口)，DOM 是 JS 和 HTML 的桥梁

### Node 节点

HTML 要先转换成 DOM 树，DOM 树就是由一个个节点组成的

node 是一个对象，有 nodeName, nodeType, parentNode, childNodes 等属性，还有 appendChild(), insertBefore() 等方法

nodeType 有十二种类型，常见的有 

- DOCUMENT_NODE
- ELEMENT_NODE
- TEXT_NODE
- DOCUMENT_FRAGMENT_NODE
- ...

可以直接用 console.dir() 直接打印 node 的属性，可以通过它的属性 parentNode 访问它的父节点，可以通过 childNodes 属性获取它的子节点。

### 常用节点类型

12种类型都是由 Node() 方法实现的，是浏览器实现的

|         类型          |          常量          | nodeType |      nodeName      |
| :-------------------: | :--------------------: | :------: | :----------------: |
|     Element 类型      |      ELEMENT_NODE      |    1     |    元素的标签名    |
|       Text 类型       |       TEXT_NODE        |    3     |       #text        |
|     Document 类型     |     DOCUMENT_NODE      |    9     |     #document      |
| DocumentFragment 类型 | DOCUMENT_FRAGMENT_NODE |    11    | #document-fragment |

### Document 类型

**不是 html 元素**

document 上挂靠了各种常用的 api

document.getElementByTagName()

document.createElement

### Element 类型

nodeName 大写的标签名

元素的 class 变成了 className，因为 class 是 js 关键字不能用

```js
// 创建一个 div 元素
var div = document.createElement('div')
div.id = 'id'
div.className = 'class'
```

技巧：在浏览器中选中了元素后，$0 就直接可以访问到这个元素，console.dir() 直接访问它的属性方法

### Text 类型

很难直接选中，在选中元素中的子节点可以看到 text 节点

```js
var text = document.createTextNode('123')
text.data = '456' // data 属性等同于 value
console.dir(text)
```

### DOM 查找

document.getElementById()

[document|Element].getElementsByClassName()

[document|Element].getElementsByTagName()

[document|Element].querySelector()

[document|Element].querySelectorAll()

querySelector querySelectorAll 传入的是 CSS 选择器

```js
var id = document.getElementById('id') // 查一个元素
var cls = document.getElementsByClassName('class') // 返回的是一个 HTML Collection，类数组，不兼容老版浏览器，要 IE8 以上
var tag = document.getElementsByTagName('li') // 返回结果类型跟 ByClassName 的一样

var qId = document.querySelector('#id') // 返回匹配的第一个结果
var qAllLi = document.querySelectorAll('li') // 返回匹配的集合
```

### DOM 新增和删除

直接操作 DOM 会导致浏览器反复渲染，可以用 DocumentFragment 来解决，DocumentFragment 是文档片段，“轻量级”的节点，nodeType = 11，nodeName = #document-fragment ，作为仓库来使用

常见的几个接口：

- `createElement(tag)` 创建元素
- `createTextNode(content)` 创建文本节点
- `appendChild(element)` 新增节点到父元素的末尾
- `insertBefore(element, target)` 新增节点到 `target` 元素的前面
- `removeChild(element)` 删除子节点

删除多个就是用循环，`element.parentNode.removeChild(element)` 连续删除就完事了


```js
var h2 = document.createElement('h2')
var h2Text = document.createTextNode('OS 列表')

h2.appendChild(h2Text)
document.body.appendChild(h2) // 添加一个节点


var ul = document.createElement('ul')
document.body.appendChild(ul)
var osList = ['mac', 'win', 'linux']
/* 
for(var i = 0, len = osList.length; i < len; i++) {
    var li = document.createElement('li')
    var liText = document.createTextNode(osList[i])
    li.appendChild(liText)
    
    ul.appendChild(li)
} 这种循环的写法不推荐
*/

var frag = document.createDocumentFragment()
for(var i = 0, len = osList.length; i < len; i++) {
    var li = document.createElement('li')
    var liText = document.createTextNode(osList[i])
    li.appendChild(liText)
    
    frag.appendChild(li) // DocumentFragment 作为仓库生成虚拟 DOM 节点
}
ul.appendChild(frag) // 一次添加多个节点

var h2 = document.querySelector('h2')
document.body.removeChild(h2) // 删除一个节点

var li = document.querySelectorAll('li')
for(var i = 0, len = li.length; i < len; i++) { // 删除多个节点
    li[i].parentNode.removeChild(li[i])
} 
```

`HTMLCollection` 的概念**一定要注意**，它是一个动态集合，当我们删除其中的某些元素的时候，再去访问，索引就会有变化！所以一般都是把它转成一个普通数组再操作。

```js
var delDom = document.getElementsByClassName('del');
delDom = Array.prototype.slice.call(delDom); // 还记得 HTMLCollection 的概念嘛？这里要转成数组！
```

### property 和 attribute

- **公认的 attribute 会映射到 property**
- 读写方式不同
- property 直接像访问对象的属性就可以读写了
- attribute 要用 getAttribute() 和 setAttribute()

property 访问 style 就是一个对象，`node.getAttirbute('style')`拿到的就是字符串，不同属性会有点差异

```js
div.custom = '111'
console.log(div) // <div>...</div>，属性只会影响 node 节点
div.setAttribute('custom', '222')
console.log(div) // <div custom="222">...<div>，特性真的会映射到 html
```

### DOM 修改样式

可以通过 style 属性来访问单个样式，也可以直接赋值样式字符串，但都不好，修改类来改变样式比较好

```js
div.style.color = 'red'
div.style.fontSize = '20px'
div.style = 'color: green; background-color: #ddd; font-size: 30px'
// 但是上面的方法一般不推荐，还是通过 class 来修改样式的比较好
div.className = 'class' // 直接修改就行了
div.className = div.className + ' newClass'
// 去除 class 和添加其实差不多，都是对字符串进行操作
// classList 是浏览器提供的操作 class 的 api,但是有一些兼容性问题
div.classList.add('happy')
div.classList.remove('class')
```

### DOM 修改内容

innerHTML 和 outerHTML 

innerHTML 是非常常用的的接口，可以直接修改 html 的内容

innerHTML 拿到的是子元素

outerHTML 拿到的是子元素和自己

### DOM 遍历

树形结构有**深度优先遍历**和**广度优先遍历**之分

```js
var domLi = document.getElementsByTagName('li')
for(var i = 0, len = domLi.length; i < len; i++) {
    console.log(domLi[i])
}
// domLi 不是数组，是 HTMLCollection，它是动态集合，它的长度可能变化的，所以要用变量 len 保存一下 domLi 的长度
```

要遍历 DOM 树一定要先知道怎么访问某个元素的子元素，某个元素的父元素

childNodes 里会有 text 节点，所以不用 childNodes 遍历，children 就没有 text 节点

访问父节点有 parentElement 和 parentNode ，常用的是 parentNode

访问兄弟节点没有直接访问所有兄弟节点的，有 previousSibling 会访问前一个节点，但是会访问到 text 节点，previousElementSibling 才是访问的前一个元素的节点，对应的访问后一个兄弟节点的有 nextSibling 和 nextElementSibling

html 里面只要有换行符就会生成 text 节点，挨着的文本节点会自动拼接成一个文本节点

#### 参考代码

```js
/**
 * 遍历函数，注意递归以及回调函数
 */
function traversal(dom, callback) {
    var len = dom.length;
    var i = 0;
    var d = null;

    for ( ; i < len; i++) {
        d = dom[i];

        callback(d);

        if (d.children) {
            traversal(d.children, callback);
        }
    }
}
/**
 * 做了个 map
 * 用好 map 可以省掉 if 条件语句
 * 大家想想，如果以后多了一种 node 节点，是这种 map 容易维护，还是 if 条件语句容易维护？
 */
var map = {
    ul: ' parent',
    li: ' child'
};

traversal(document.querySelector('#list').children, function(node) {
    node.className = node.className + ' ' + map[node.nodeName.toLowerCase()];
});
```

