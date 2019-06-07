需要一个本地服务器

http-server 静态服务器

## AJAX

Asynchronous Javascript + XML 所以叫 AJAX，不过现在数据格式一般是 json

```javascript
function getData(callback) {
    var xhr = new XMLHttpRequest()
    
    xhr.onreadystatechange = () => { //监听请求状态
        console.log('xhr after response', Object.assign(xhr))
        
        if(xhr.readyState === 4) {
            callback(JSON.parse(xhr.responseText), xhr) // JSON.parse反序列化字符串
        } else {
            console.log(xhr.status)
        }
    }
    
    xhr.open('get', 'data.json') // 打开连接
    xhr.send(null) // 发送请求
}
```

- `new XMLHttpRequest()` 新建连接
- `xhr.open()` 打开连接
- `xhr.send()` 发送数据
- 接收数据

监听 xhr.readyState

- 新建连接 0
- 打开连接 1
- 发送数据 2
- 接受数据 3 是开始接受，4 是接受完成

# JSON

> 前端新手对 JSON 的理解都是错误的。 — 让人头疼的小江

官网的第一句话：

> **JSON** (JavaScript Object Notation) is a lightweight data-interchange format.

JSON 是一种轻量级的数据交换格式。JSON 是一种格式，而不是对象，所以别再说“这个 JSON 对象有问题”这种话。

```js
var a = { // 这是一个对象字面量，和 JSON 无关
    "hello": "JSON"
};

var b = [ // 这是一个数组字面量，和 JSON 无关
    {
        "hello": "JSON"
    }
];

var c = '{"hello":"JSON"}'; // 这是一个字符串变量，和 JSON 有一点关系
{"hello":"JSON"}
```

这才是 JSON ，JSON 就是文本，和什么语言没有关系，JS 可以解析，自然 JAVA、Python 都可以解析，所以`{"hello":function () {}}`不是一个 JSON 是不是就很容易理解了，你这个 `function`放在 JAVA 里面算个什么回事？！

当然，它是有格式的文本，至于什么格式，看官网 <http://json.org/> 就好了。

“让人头疼的小江大哥，后台同学返回的 JSON 数组有问题啊。”

“ (⊙ˍ⊙)”

## 同源策略

same-origin policy

**不同域**的客户端**脚本**在没有明确授权的情况下，不能**读写**对方的**资源**

同协议、同域名、同端口

### CORS

Cross-Origin Resource Sharing 跨域资源共享

有浏览器兼容问题

### JSONP

JSON with Padding

就是用 `script` 标签

缺点也很明显，只能是 get 请求