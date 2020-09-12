react 项目里不会有 XSS 问题，除非使用 `dangerouslySetInnerHTML`

## XSS 简介

XSS 攻击，即跨站脚本攻击（Cross-Site Script）

大致流程为：

1. 坏人网页里**注入**恶意脚本代码
2. 当一般用户访问时获取到包含恶意代码的网页
3. 通过恶意脚本，坏人可以获取和控制用户信息

XSS 攻击根据不同的注入和攻击形式，通常划分为以下三种类型：

- 反射型（非持久型）XSS
- 存储型（持久型）XSS
- DOM-Based 型 XSS

### 反射型（非持久型）XSS

反射型 XSS 诱导用户点击恶意链接来造成一次性攻击

1. 一般用户访问带有恶意脚本代码参数的 URL
2. 服务器获取请求参数且**直接**使用
3. 服务器**反射**会结果页
4. 用户中招

特点：

- 一次性（非持久性）

- 通过用户点击链接引起（所以一般会对链接进行修饰）

备注：

Chrome 都内置了 相应的 **XSS 过滤器**，来防范用户点击了反射型 XSS 的恶意链接，反射型 XSS 归根到底就是由于不可信的用户输入被服务器在没有安全防范处理，然后直接使用响应到页面中，然后反射给用户而导致代码在浏览器执行的一种 XSS 漏洞

### 存储型（持久型）XSS

恶意代码**存储**到漏洞服务器中，用户浏览相关页面发起攻击

1. 将恶意代码上传或存储到漏洞服务器上
2. 一般用户访问相关恶意脚本的页面
3. 服务器读取恶意数据且直接使用
4. 服务器未防范返回含有恶意脚本的页面
5. 用户中招

特点：

- 持久化（存储在服务器）
- 不需要交互也可以触发

**强调**：

无论是反射型 XSS 还是存储型 XSS，其本质都是 XSS 攻击。因此大家需要始终记住 **XSS 攻击的本质就是由于浏览器执行攻击者插入的恶意的脚步数据所致**。

### DOM-Based 型 XSS

基于浏览器 **DOM 解析**的攻击

1. 用户打开带有恶意的链接
2. 浏览器（客户端）在 DOM 解析时直接使用恶意数据
3. 用户中招

常触发场景：

innerHTML、outHTML、document.write

对于 **DOM-Based 型 XSS** 来说，其并不依赖服务端，服务端的响应不会涉及到恶意脚本的内容。**DOM-Based 型 XSS** 是客户端 XSS 的一种形式, 其数据来源在客户端中。其中 **DOM-Based 型 XSS** 是由于客户端 JavaScript 脚本修改页面 DOM 结构时引起浏览器 DOM 解析所造成的一种漏洞攻击。因此如果页面 JavaScript 脚本不存在着漏洞的话，则不会发送 **DOM-Based 型 XSS** 攻击

## XSS payload 和危害

实现 XSS 攻击的恶意脚本

- 窃取用户 cookie	document.write
- 识别用户浏览器	navigator.userAgent
- 伪造请求			get / post
- XSS 钓鱼			XSS payload + 钓鱼网站
  - XSS 在页面注入钓鱼网站链接
  - 用户浏览钓鱼网站
  - 用户输入账号密码
  - 账号密码发送到服务端

## XSS 防御 - httpOnly

给 cookie 设置 httpOnly（阻止客户端脚本访问 cookie），只有在发送请求时才会带上和使用该 cookie

缺点：

- 自己也不能用
- 非根本性解决 XSS

设置 Cookie 的 httpOnly 属性时，浏览器会禁止客户端脚本去获取和修改，只有在发送请求时浏览器才会使用，从而减少 Cookie 被盗取的可能性。但由于网站本身可能也需要获取 Cookie 的信息，因此通常来说，我们只会在部分重要的 Cookie 上设置 httpOnly 属性。

## XSS 防御 - 输入检查

永远不相信用户的输入

- 判断输入格式（白名单）

- 过滤危险字符 去除` <script>`、javasciprt、onclick 等（黑名单）

- 转义特殊字符 （黑名单）

  - `<`	`&lt;`
  - `>`	`&gt;`
  - `&`	`&amp;`
  - `\`	`&quot;` 
  - ...

要前端和服务端都进行输入检查，因为黑客可以绕过前端向服务端发送请求

`/<(script|style|iframe)[^<>]*?>.*?<\/\1>/ig`

`/(onerror|onclick)=([\"\']?)([^\"\'>]*?)\2/ig`

### 拓展

在真实的业务开发中，输入检查所做的工作更为全面和复杂。目前有许多十分完善通用的 XSS 防范的代码库，大家可以结合理解去阅读和试用下：

- [node-esapi](https://github.com/ESAPI/node-esapi/blob/master/lib/esapi.js)
- [js-xss](https://github.com/leizongmin/js-xss)

## XSS 防御 - 输出检查

XSS payload 

- HTML 执行环境 需要 HtmlEncode()
  - HTML 标签中
  - HTML 属性中
- js 解析环境 需要 JavascriptEncode()
  - script 标签中
  - 事件属性中
- URL 解析环境 需要 URLEncode()
  - URL 中

**需要始终记住的是，之所以会发生 XSS 攻击，就是由于用户的输入被当做代码来执行，导致出现意料之外的运行结果**，所以要将用户的输入变成普通字符来避免被攻击