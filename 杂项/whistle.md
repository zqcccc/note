# whistle

## 前言

在试过了 charles，spy-debugger 等一系列的抓包工具后，各有各的一些小痒点不能解决，比如 charles 更像个抓包 gui，大多数配置都是靠点选，在一些较为复杂的情况下并不灵活，spy-debugger 功能较单薄，直到用了 whistle 后，功能强大的同时上手也比较简单。

还有每次连接抓包服务的时候，繁琐地修改代理导致每次抓包都是抗拒的，这里也会用一些软件来做到一键连接代理。

[whistle官网安装教程](https://wproxy.org/whistle/install.html)

这篇文章呢，如果就照搬官网的文章那也太没诚意了，这里主要讲一些提效的小技巧，跟官方一样的内容不会提太多，学会按照官方的教程去一步一步完成，遇到大多数问题能自己解决也是很重要滴，这款工具很强大，即使安装过程有点小繁琐也是值得的（charles 安装过程其实也是差不多的，只要你要抓包，下面的步骤怎么都得走）

以下内容请配合官网教程食用

## 安装与运行

1. 安装 node (建议用 nvm 管理和安装)
2. 安装 whistle

```shell
npm install -g whistle
```

安装完成后直接就可以启动了

```shell
w2 start
```

当然这里推荐同时使用 socks 启动

```shell
w2 start -p 8899 --socksPort 8900
```

这个命令不太可能总是去手动输入，可以考虑放到命令行配置别名中

```bash
alias sw="w2 start -p 8899 --socksPort 8900" #开启whistle服务
```

> 平时在开发项目的时候也是这样，比如说 npm run start 对于前端来说可能是天天都跑的命令，取一个别名并记住很有必要，学点命令行真的很提效

这样在命令行只要输入 `sw` 就能同时启动 http 和 socks 两种方式，当然这个 `sw` 可以改成你自己想要的任何名字哈，至于这里为什么要用 socks 启动，看到后面就知道啦

第一次运行的话，是需要给电脑安装证书的，[官网也有教程哈](http://wproxy.org/whistle/webui/https.html)

到了这步 whistle 就安装启动好了，剩下的事情都是客户端的事情了

## 客户端代理

官网教程里提到了浏览器代理是用 [SwitchyOmega](https://chrome.google.com/webstore/detail/padekgcemlokbadohgkifijomclgjgif) 这个插件挺简单的，也是点一下就切换，挺好用的，因为平时电脑科学上网也是用的这个。

比较麻烦的就是手机端的部分，开启 whistle 服务的机器和手机客户端要在同一局域网中，按照官网的描述也是可以达到连接代理的效果，但是呢，每次都手动去 wifi 设置里去输入服务的 ip 和端口是最劝退我们的一步，下面的步骤可能有点繁琐，但是都是做一两遍就越来越娴熟的

### iOS

ios 的代理软件特别多，但是因为特殊原因只能在非国区商店里下载到靠谱的代理软件，这里以小火箭为例

<img src="../media/shadowrocket_w2.jpeg" alt="shadowrocket配置" style="zoom: 25%;" />

几乎所有的这些软件配置都是大差不差的

配置好就能直接连接了，第一次连接的手机是要下载证书的哈，[官网也有教程哈](http://wproxy.org/whistle/webui/https.html)，这个证书是在电脑上生成的自签证书，所以是要连接上 whistle 代理才能下载的哦，顺带提一句，新版 ios 有两个地方要开启（有次因为只开了其中一个折磨了我整个下午）：

-  “设置” > “通用” > “VPN与设备管理”（也可能叫描述文件） > “whistle.开头的一个文件” > 信任还是啥玩意，点一下就行
- “设置” > “通用” > “关于本机” > “证书信任设置”  > 勾选上 “whistle.开头的那个”

小火箭和 Potatso lite 是我同时用的，因为总是在机场和开发代理之间切换也非常的麻烦，现在随便一个机场都是几十个服务器选项，所以我建议是科学上网用一个，开发代理用另一个

### 安卓

安卓我尝试了非常多的软件，大多是不支持或者不是很好用，这里就找了一个 app 叫 `kitsunebi`，打开后点击右上角的添加节点手动添加就可以了，不过它只支持 socks 协议，这也是为什么我们需要 whistle 通过 socks 的方式开启，对于我们前端开发来说，ios 和安卓的 webview 可能不一样，需要我们都学会在不同的手机上调试

[下载地址](https://github.com/zqcccc/wechat-mall/releases/download/0.0.2/fun.kitsunebi.kitsunebi4android_83_apps.evozi.com.apk)，app 包下载完了通过各种方式再传给手机，这里推荐用 adb 安装

adb 在平时往安卓手机中传个什么或者装个包都是非常方便的，[adb 官网](https://developer.android.com/studio/command-line/adb)下载，安装有问题请 google，adb 安装完成后，手机可能需要开启开发者模式并通过数据线连接电脑并信任电脑

```shell
adb install /Users/xxx/Downloads/fun.kitsunebi.kitsunebi4android_83_apps.evozi.com.apk
```

`/Users/xxx/Downloads/fun.kitsunebi.kitsunebi4android_83_apps.evozi.com.apk` 这是在电脑上的 app 包的位置，执行命令后就提示成功就会装上 app 了

> 有些安卓在第一次装证书的时候可能比较麻烦，就是通过浏览器下载了证书，但是不能直接打开，要到系统设置里去找到证书安装的入口才能安装，但是在这个地方呢，它会大概会打开一个文件管理并直接定位到 sdcard 的位置，这个时候如果不是很熟悉自己手上的手机或者安卓的人，根本不知道去哪里找刚刚在浏览器中下载的证书，这里我建议你直接用 adb 推到安卓手机的 sdcard 中，这样找起来起码简单点吧
>
> ```shell
> adb push /Users/xxx/Downloads/rootCA.crt  /sdcard/rootCA.crt
> ```
>
> `/Users/xxx/Downloads/rootCA.crt` 是在电脑上的证书路径，这个证书直接在 whistle 的网页上就能下载下来了
>
> `/sdcard/rootCA.crt` 就是我们要保存到手机上的位置

证书装好了就可以开始抓包啦

## whistle 使用

基础用法看官方说明就行了，下面说几个常用的功能

### 响应替换

其实认真看一下官网的教程就很好用了，比较常用的是替换响应结果，先抓到接口的响应，复制下来修改后到 左侧栏 `Values` 里 create 一个值比如叫 `cert_info`，然后粘进去刚刚修改的响应结果，在 `Rules` 里配置规则，比如说：

```
https://xxx.com/cert-info/detail tpl://{cert_info} resCors://enable #实名认证
```

这样请求 `https://xxx.com/cert-info/detail` 的结果就是刚刚被你修改的值，`resCors://enable` 是开启跨域，前端 h5 是经常用到的

其实这个 value 不光是一个 json 响应，[几乎是啥都能写](http://wproxy.org/whistle/frequet.html)，很强大

配置规则，对某个 html 直接开启 weinre 调试

```
https://xxx.com/index.html weinre://123
```

这样在访问这个 HTML 的时候，直接就开启了 weinre，在网页的 `Weinre` 里点那个 `123` 就会打开一个新的 weinre 页面，

>  在用 weinre 调试的时候记得把你页面的监控上报对匹配到 `/\.whistle-path/` （也可能不叫这个名字）路径的 api 停止上报，不然你的页面请求上报可能直接报警

### 内容搜索

直接在 `Network` 面板的最下面输入就能搜索 url 了，但是更多的情况是我们要搜索响应中的内容

比如说你知道某个接口应该是返回了一个 `商城` 的字符串你只要搜

```
body:商城
```

就能搜到你要找的接口，定位问题必备技能

### 插件的使用

以集成 vConsole 为例，也只是一行规则的事，[插件地址](https://github.com/whistle-plugins/whistle.inspect)

1. 确保 whistle 是最新版
2. 安装插件

```
w2 i whistle.inspect
```

3. 在 `Plugins` 中确认插件是勾选了的状态（勾选了才是开启了）
4. 在 `Rules` 中对需要开启 vConsole 的页面添加 `pattern whistle.inspect://`，例如：

```
https://xxx.com/index.html whistle.inspect://
```

5. 访问页面就能看到 vConsole 开启了

