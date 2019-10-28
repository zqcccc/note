## 认识 HTML
- 在浏览器 ( Chrome ) 查看 HTML
    - 在打开的页面鼠标右击，选择 “查看网页源代码” 或者 “检查”
    - 使用 **F12** 快捷键查看 HTML 源码
- 最简单的 HTML 代码

    ```HTML
    <!DOCTYPE html>
    <html>
        <head>
            <meta charset="utf-8">
            <title>基本 HTML 结构</title>
        </head>
        <body>
            <h1>我是一级标题</h1>
            <p>我是一段文本段落</p>
        </body>
    </html>
    ```

## HTML 基础结构

![HTML 基础结构](https://ws1.sinaimg.cn/large/b7f2e8afgy1fvpf1ifr37j20ed09rjrc.jpg)


### 1. 文档声明
- 作用
    - 告诉浏览器使用哪个 HTML 版本进行解析  (**千万不能忘记**)
- 常用文档声明

```HTML
<!-- html5 文档声明 -->
<!DOCTYPE html>

<!-- html4 文档声明  -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
```

### 2. html 元素（ 顶级元素或根元素 ）
- 注意：
    - html 元素**只能包含 head 元素或者 body 元素**

### 3. head 元素
- 作用
    - 用于定义网页的常规信息和元数据

### 4. body 元素
- 作用
    - 存放页面的内容

## 深入了解 head 元素
### head 元素的三大功能
- 描述网页基本信息
    - 文档标题（浏览器标签中显示的文本）: `<title>深入了解 head 元素</title>`
    - 编码格式: `<meta charset="utf-8">`, _不推荐使用功能 gb2312_
    - 视窗设置：`<meta name="viewport" content="width=device-width, initial-scale=1.0">`
    - 搜索引擎优化相关内容：`<meta name="description" content=“帮助你深层次了解HTML文档结构”>`
    - IE 浏览器版本渲染设置: `<meta http-equiv="X-UA-Compatible" content="ie=edge">`
    - .....
- 指向渲染网页需要其他文件链接
    - CSS 文件: `<link rel="stylesheet" type="text/css" href="style.css">`
    - JavaScript 文件: `<script src=“script.js"></script>`
- 各大厂商根据自己需要定制
    ```html
    <!--  360 浏览器定制 -->
    <meta name="renderer" content="webkit"> <!-- 默认webkit内核 -->
    <meta name="renderer" content="ie-stand"> <!-- 默认IE标准模式 -->
    <meta name="renderer" content="ie-comp"> <!-- 默认IE兼容模式 -->
    
    <!--  qq 分享定制， 参考 https://open.mobile.qq.com/api/mqq/index -->
    <meta itemprop="name" content="这是分享的标题"/>
    <meta itemprop="image" content="http://imgcache.qq.com/qqshow/ac/v4/global/logo.png" />
    <meta name="description" itemprop="description" content="这是要分享的内容" />
    
    <!--  iOS 定制 -->
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    <link rel="apple-touch-icon" href="touch-icon-iphone.png">
    <link rel="apple-touch-startup-image" href="/startup.png">
    ```

#### 参考链接
- [HTML meta标签总结与属性使用介绍](https://segmentfault.com/a/1190000004279791)
- [移动端重构系列2——新建空白页面](https://www.w3cplus.com/mobile/mobile-terminal-refactoring-create-page.html)