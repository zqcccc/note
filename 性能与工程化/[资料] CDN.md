# CDN 相关

为了提高网页应用的加载速度，我们会使用到浏览器缓存，然而浏览器缓存只能提升二次访问的速度。对于加速首次访问的速度，我们最常见的手段则是使用 CDN 加速。

CDN 的全称是 **Content Delivery Network**，即内容分发网络。通过将静态资源缓存到离用户很近的相同网络运营商的 CDN 节点上，使用户可以就近取得所需的内容，解决网络拥塞状况，提高用户访问网站的响应速度。

![img](http://coding.imweb.io/img/p6/cdn.png)

事实上，在一些较大型的公司来说，通常是由公司的运维开发人员来负责部署 CDN 服务的，前端人员只需要了解使用 CDN 服务的大致流程即可。

## 1 选择 CDN 供应商

事实上，现在还有许多的 CDN 服务厂家，我们需要根据需求的情况去选择相应的厂商。现阶段 CDN 云服务商的竞争已经由 **技术** 逐渐取代**价格**成为 CDN 厂商在客户谈判时最关键因素，技术更高的 CDN 服务能够降低带宽成本、提升用户体验。

如今 CDN 各大厂商都会提供十分详尽的接入文档，使我们接入的开发成本变得更低。这里列举国内比较出名的 CDN 厂商：

- [叉拍云](https://www.upyun.com/products/cdn?gclid=EAIaIQobChMImayU6ejE1QIVHL69Ch19bANOEAAYAyAAEgLGY_D_BwE)
- [腾讯云](https://cloud.tencent.com/product/cdn?fromSource=gwzcw.251481.251481.251481&gclid=EAIaIQobChMIpLnk7eHE1QIVVQoqCh3JhwO3EAAYAiAAEgKBJ_D_BwE)
- [七牛云](https://www.qiniu.com/products/fusion?utm_campaign=googleSEM&utm_source=googleSEM&utm_medium=googleSEM&utm_content=googleSEM&gclid=EAIaIQobChMIpLnk7eHE1QIVVQoqCh3JhwO3EAAYASAAEgIDpvD_BwE)
- [阿里云](https://www.alibabacloud.com/zh?utm_content=se_712562&gclid=EAIaIQobChMIiI7yienE1QIVjgYqCh1RPgD_EAAYASAAEgJRwfD_BwE)

## 2 连接 CDN 服务

通常购买了 CDN 服务后，该 CDN 服务会提供相应的接口让我们去上传和部署我们的资源。这部分的工作通常是由我们运维人员去负责的，对于一些小公司可能则需要前端或者后台人员去负责连接 CDN 服务，使业务能够部署静态资源到相应的服务器上。

## 3 前端工程静态资源部署

使用 CDN 服务，对前端工程产生了一定的影响，我们需要对静态资源的部署做两项改变：

### 3.1 将静态资源部署到不同网络线路的服务器中

由于用户的网络线路有很多种，如网通、电信、移动等，为了让不同地区的用户会访问到离自己最近的相同网络线路上的 CDN 节点。因此我们需要将我们的资源部署到不同的网络线路的服务器中，然后通过这些服务器同步到不同线路的 CDN 节点上。

### 3.2 加载静态资源时需使用与页面不同的域名

使用不同的域名来加载 CDN 服务的静态资源，一方面便于接入为 CDN 时设置 DNS 的智能解析。另一方面当静态资源和主页面使用不同的域名，在加载静态资源时 HTTP 请求并不会带上主域页面的 Cookie 等数据，减少了请求时的数据传输量，进一步加快网络访问。

### 3.3 资源路径的修正

通常情况下，我们的资源都是在本地，比如有个 JS 文件，我们在 HTML 里面按照相对目录引用它：

```html
<script src="js/script.js"></script>
```

使用了 CDN 的话，资源都部署到了 CDN 服务器上去咯，这个时候我们必须用绝对路径来引用：

```html
<script src="http://xxx.xxx.xxx/js/script.js"></script>
```

所有啊，必须有个修正资源路径的过程，而这个过程当然不能手动，通常都是在工程化里面解决这个问题。

## 总结

如今 CDN 服务基本上已经成为现代大型网页应用的标配。这项技术是一种十分常用的网络性能优化手段。在未来的前端开发中，我们会经常与 CDN 打交道。因此如果能够清楚了解了 CDN 服务的相关原理和概念，将会让我们在将来的开发中能够更加得心应手。

## 更多阅读

- [盘点国内网站常用的一些CDN公共库加速服务](https://yusi123.com/3093.html)