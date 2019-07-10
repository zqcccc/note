在 centos 7 下学习

环境

```shell
yum -y install gcc gcc-c++ autoconf pcre-devel make automake
yum -y install wget httpd-tools vim
```

**基于Yum的方式安装Nginx**

我们可以先来查看一下yum是否已经存在，命令如下：

```shell
yum list | grep nginx
```

如果不存在，或者不是你需要的版本，那我们可以自行配置yum源，我们可以放心大胆的使用官网提供的源。

```shell
vim /etc/yum.repos.d/nginx.repo
```

然后把代码复制进去，这里你可能需要一些Vim的操作知识，如果不熟悉，可以自行学习一下，当然我视频中也是有讲解的。

你可以根据你的系统或需要的版本进行修改。

如果都已经准备好了，那就可以开始安装了，安装的命令非常简单：

```shell
yum install nginx
```

安装完成后可以使用命令，来检测Nginx的版本。

```shell
nginx -v
```

`nginx -q 包名` 查看包版本，`-ql` 列出所有相关包的版本

直接运行 `nginx` 命令就可以开启 nginx 服务了，一般没有什么提示就说明服务开启成功了

**使用systemctl命令启动**

还可以使用个Linux的命令进行启动，我一般都是采用这种方法进行使用。因为这种方法无论启动什么服务，都是一样的，只是换一下服务的名字（不用增加额外的记忆点）。

```shell
systemctl start nginx.service
```

输入命令后，没有任何提示，那我们如何知道 Nginx 服务已经启动了哪？可以使用 Linux 的组合命令，进行查询服务的运行状况。

```shell
ps aux | grep nginx
```

然后显示的信息中有一条即使 nginx 关闭也是存在的

```shell
nginx -s quit
```

| 名称                      | 命令                    |
| ------------------------- | ----------------------- |
| 启动nginx                 | start nginx             |
| 修改配置后重新加载生效    | nginx -s reload         |
| 重新打开日志文件          | nginx -s reopen         |
| 测试nginx配置文件是否正确 | nnginx -t -c nginx.conf |
| 快速停止nginx             | nginx -s stop           |
| 完整有序的停止nginx       | nginx -s quit           |

`killall nginx` 直接杀死进程也可以

`systemctl stop nginx.service` 也可以停止进程

配置文件内容：

在 location 中可以设置 `deny ip;` 来禁止某些 ip 访问

可以 `allow ip;` 和 `deny all` 来只让某些 ip 访问

而且 allow 要在 `deny all` 前面执行才会生效，配置文件是从上往下执行的，只要上面的指令执行了过了，下面的指令就不会生效

反向代理：

正向反向都是相对于客户端来说的，一般翻墙就是正向代理，而去代理服务器就是反向代理，反向代理一般可以提高安全性，其次实现负载均衡等等很多好处

`proxy_pass` 就可以设置代理地址

**其它反向代理指令**

反向代理还有些常用的指令，我在这里给大家列出：

- proxy_set_header :在将客户端请求发送给后端服务器之前，更改来自客户端的请求头信息。
- proxy_connect_timeout:配置Nginx与后端代理服务器尝试建立连接的超时时间。
- proxy_read_timeout : 配置Nginx向后端服务器组发出read请求后，等待相应的超时时间。
- proxy_send_timeout：配置Nginx向后端服务器组发出write请求后，等待相应的超时时间。
- proxy_redirect :用于修改后端服务器返回的响应头中的Location和Refresh。

适配 PC 或移动设备：

根据 `$http_user_agent` 来判断是否开启移动端

**gzip最简单的配置**

```ngin
http {
   .....
    gzip on;
    gzip_types text/plain application/javascript text/css;
   .....
}
```

**gzip的配置项**

Nginx提供了专门的gzip模块，并且模块中的指令非常丰富。

- gzip : 该指令用于开启或 关闭gzip模块。
- gzip_buffers : 设置系统获取几个单位的缓存用于存储gzip的压缩结果数据流。
- gzip_comp_level : gzip压缩比，压缩级别是1-9，1的压缩级别最低，9的压缩级别最高。压缩级别越高压缩率越大，压缩时间越长。
- gzip_disable : 可以通过该指令对一些特定的User-Agent不使用压缩功能。
- gzip_min_length:设置允许压缩的页面最小字节数，页面字节数从相应消息头的Content-length中进行获取。
- gzip_http_version：识别HTTP协议版本，其值可以是1.1.或1.0.
- gzip_proxied : 用于设置启用或禁用从代理服务器上收到相应内容gzip压缩。
- gzip_vary : 用于在响应消息头中添加Vary：Accept-Encoding,使代理服务器根据请求头中的Accept-Encoding识别是否启用gzip压缩。