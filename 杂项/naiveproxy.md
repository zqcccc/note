---
title: "NaïveProxy 搭建过程"
date: "2022-11-13"
description: "naiveproxy 搭建过程"
---

naiveproxy 是非常不错的伪装代理，用来富强是非常好的一个选择，除了现在客户端应用比较少这个缺点以外

naiveproxy 搭建过程

[项目地址](https://github.com/klzgrad/naiveproxy)

一、安装 go

```shell
wget https://go.dev/dl/go1.19.3.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.3.linux-amd64.tar.gz
# export PATH=$PATH:/usr/local/go/bin
echo 'export PATH=$PATH:/usr/local/go/bin' >> $HOME/.profile
source $HOME/.profile
go version # 查看 go 指令是否能正常运行
```

二、编译 caddy

```shell
mkdir naive && cd naive
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
~/go/bin/xcaddy build --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive
```

三、配置文件

```conf
:443, example.com # 修改网址，已经提前指向本机
tls me@example.com
route {

  forward_proxy {
    basic_auth username password # 修改用户名和密码
    hide_ip
    hide_via
    probe_resistance
  }
  reverse_proxy  https://demo.cloudreve.org  { # 伪装网址
   header_up  Host  {upstream_hostport}
   header_up  X-Forwarded-Host  {host}
  }
}
```

四、配置 systemd 开机自启

[参考](https://github.com/klzgrad/naiveproxy/wiki/Run-Caddy-as-a-daemon)

```shell
chmod +x caddy
mv caddy /usr/bin/

mkdir /etc/caddy
mv Caddyfile /etc/caddy/

groupadd --system caddy
useradd --system \
    --gid caddy \
    --create-home \
    --home-dir /var/lib/caddy \
    --shell /usr/sbin/nologin \
    --comment "Caddy web server" \
    caddy
```

五、在 `/etc/systemd/system/` 下新增 `caddy.service` 

```
[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target

[Service]
User=caddy
Group=caddy
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

保存文件后，启动服务

```sh
systemctl daemon-reload
systemctl enable caddy
systemctl start caddy
```

检查服务状态

```sh
systemctl status caddy
```

重载配置

```sh
systemctl reload caddy
```

五、客户端配置

[下载地址](https://github.com/klzgrad/naiveproxy/releases) 下载对应的包解压后，修改 config.json 后，运行主程序就行

默认配置地址是 `https://user:pass@example.com`，`quic://user:pass@example.com` 是用 quic 协议的写法

Windows 也可以用 v2rayN 的客户端，不过自定义配置好像有一些 bug 就不折腾了，手机用小火箭直接就可以配置，我的其他设备是由软路由通过 vmess 到电脑 v2rayN 再从 socks5 到 naiveproxy 客户端的，之所以设置软路由走电脑的客户端是为了切换方便，因为软路由是在路由器上的，路由器是 100 多块钱买的红米 AC2100，每次打开软路由的页面都非常的慢，体验很差，加之前段时间封地厉害，于是换成了电脑来切换节点，当然这台电脑是被排除在软路由的代理之外的。
