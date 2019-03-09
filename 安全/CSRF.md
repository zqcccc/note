## 基本原理

Cross Site Request Forgery 跨站请求伪造

攻击流程：

1. 用户登录银行网站 假设 bank.com
2. 网站会给用户 setCookie（然后用户的电脑上就有了 bank.com 的 cookie）
3. 黑客诱导一般客户访问他设置好的网站 比如 evil.com
4. 返回页面（伪造发送到 bank.com 的转账请求）
5. 这时转账请求就发给了 bank.com

## CSRF 防御

- 用户不知情
  - 使用验证码（不常用，影响用户体验）
- 跨站请求
  - 使用 referer 验证（不可靠，可能被篡改）
- 参数伪造
  - anti CSRF token 验证（现在最常用，最有效的方法）需要前后端协作

## XSS + CSRF

XSS 和 CSRF 可以配合一起攻击，相互之间是有影响的

如果在 bank.com 上就有 XSS 漏洞，那么 referer 和 anti CSRF token （算法加密的 token 在前端被一览无遗，又被破解的可能）都失效了，使用验证码也可能被暴力破解

## XSS 蠕虫

XSS 蠕虫需要有用户交互界面

发一条带恶意脚本的播客，然后别人看到也会复制这个脚本再发一个相同的恶意脚本的播客，这就形成了**不断传播的 XSS + CSRF 攻击**，影响范围非常广

只要做好站点的安全防御，把 XSS 和 CSRF 的防范好了，就可以防止 XSS 蠕虫的出现

