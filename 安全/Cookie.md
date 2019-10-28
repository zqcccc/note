## 简介

“您上次观看至 XX:XX 处，正在为您续播” 

很多类似的这种本地存储都是 cookie 做到的

js 的变量可以保存信息，但是变量是有生命周期的，一旦页面刷新，包括全局变量在内的所有变量都会销毁，所以变量没法保存持久化的信息。而 cookie 实际上是在保存在浏览器本地的信息，它们储存的信息会保存在本地文件中，就算页面刷新了，信息也还是存在

通过键值对的方式存储信息

```
Domin = ke.qq.com					域
Expires = 2018-09-05T 12:32:07.203Z 过期时间
Max-Age = 36000  					距离过期的秒数，有兼容性问题
Path = /							路径
Secure								只有在 HTTPS 下才能发送
HttpOnly							不能通过 JS 的 document.cookie 访问
```

session cookie（临时 cookie）没有设置过期时间的 cookie 叫临时 cookie，关闭浏览器就会失效

permanent cookie（持久 cookie）

我们可以通过 `Expires` 字段设置 cookie 过期时间，设置了过期时间的 cookie 就是持久化 cookie， 只有超过过期时间 cookie 才会失效，关闭浏览器并不会使持久化 cookie 失效

cookie 是不能跨域的，domain 不可以设置成跨域的其他域名

cookie 虽然可以储存本地信息，但是不适合储存大量的信息，因为 cookie 还有个特点是每次请求都会带上这个域相关的所有 cookie，就是说，cookie 会影响浏览器请求的大小，因此我们要尽量保持 cookie 体积小，还有尽量用于保存跟用户相关的信息。同时，浏览器本身对 cookie 总大小也有限制，目前是不允许超过 4k，超过的会丢失。

## js 操作 cookie

document.cookie 就可以访问 cookie

```js
function getCookie(cname) { // 获取 cookie
    var name = cname + "="
    var decodedCookie = decodeURIComponent(document.cookie)
    var ca = docodedCookie.split(';')
    for (var i = 0; i < ca.length; i++) {
        var c = ca[i]
        while (c.charAt(0) == ' ') {
            c = c.substring(1)
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length)
        }
    }
    return ''
}
// 如果 cookie 是这样的：name1 = value1; name2 = value2
getCookie('name1') // value1

// 添加 cookie
document.cookie = encodeURIComponent('username') + '=' + encodeURIComponent('mike') + ';' + 'domain=ke.qq.com' + '; expires=' + new Date('2019-1-1')

// 删除 cookie
document.cookie = encodeURIComponent('username') + '=' + encodeURIComponent('mike') + ';' + 'domain=ke.qq.com' + '; expires=' + new Date(0) // 1970-1-1,给一个过期时间就可以删除 cookie
```

domain 不能跨域

path 一般要设置成 `/` 不然就是设置时的当前路径

因为原生操作 cookie 的方法比较奇怪，所以一般用第三方库

## 使用 cookie 保存用户信息

http 是无状态的