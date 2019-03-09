### window

ECMScript 的 global 对象，全局作用域

系统对话框 alert confirm prompt

- alert 弹出提示的窗口
- confirm 弹出的窗口选择是否，返回 true 或 false
- prompt 弹出的窗口可以输入值，返回输入的值

### location

提供了文档相关的信息，提供导航

归属于 window 和 document

属性和方法：

- href
- path
- assign() 可以加载别的页面，跟直接修改 href 效果一样
- reload() 重载页面，`location.reload(true)` 不从缓冲中重载页面

### navigator

客户端相关的信息

userAgent 浏览器的用户代理字符串

platform 浏览器所在的系统平台

### history

保存着用户上网的记录，从窗口被打开的那一刻算起

- length 历史记录的数量
- go() 跳转到指定历史记录
- forward() 前进，相当于 go(1)
- back() 后退，相当于 go(-1)