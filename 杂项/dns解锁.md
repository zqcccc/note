---
title: vps 解锁网飞
date: "2022-11-02"
description: "一般的 vps 通过三方的服务来解锁网飞等流媒体"
---

一般的 vps 并不能看网飞自制剧，可以通过 warp 来解锁，但是经常要换 ip，且延迟和速度都让人觉得很慢，通过三方的 dns 服务来解锁网飞等流媒体价格并不算贵，速度会快很多

[参考文档](https://doc.nfdns.top/pages/052b93/#%E9%9D%9Ev2ray%E7%9B%B8%E5%85%B3%E5%8D%8F%E8%AE%AE%E7%AE%80%E5%8D%95%E6%96%B9%E6%B3%95-%E7%9B%B4%E6%8E%A5%E4%BF%AE%E6%94%B9vps%E7%9A%84%E7%B3%BB%E7%BB%9Fdns)

需要有一定使用基础，此处以 ubuntu/debian 系统为例

一.安装 dnsmasq

```shell
apt-get -y install dnsmasq
```

安装完可能并不会成功运行，如果本地有 dns 服务，它一般会监听本地的 53 端口，找到服务然后关掉

我遇到的情况就是 Ubuntu 貌似用的是 `systemd-resolved` 来管理 `/etc/resolv.conf` 的，如果你不是的话，可以跳过这几个命令

```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
# systemd-resolved 的 /etc/resolv.conf 的 link
mv /etc/resolv.conf /etc/resolv.conf.link
```

二.配置 dnsmasq

1.配置文件/etc/dnsmasq.conf

```shell
vi /etc/dnsmasq.conf
```

```conf
#具体需要添加的域名请在TG群发送“域名规则”获取。4.4.4.4以实际DNS为准。

server=/amazon.com/4.4.4.4
server=/awsstatic.com/4.4.4.4
server=/amazonaws.com/4.4.4.4
server=/edgekey.net/4.4.4.4
server=/edgesuite.net/4.4.4.4
server=/edgecastcdn.net/4.4.4.4
server=/fast.com/4.4.4.4
server=/netflix.ca/4.4.4.4
server=/netflix.com/4.4.4.4
server=/netflix.net/4.4.4.4
server=/nflximg.com/4.4.4.4
server=/nflximg.net/4.4.4.4
server=/nflxvideo.net/4.4.4.4
server=/nflxso.net/4.4.4.4
server=/nflxext.com/4.4.4.4
server=/nflxsearch.net/4.4.4.4
server=/netflixdnstest1.com/4.4.4.4
server=/netflixdnstest2.com/4.4.4.4
server=/netflixdnstest3.com/4.4.4.4
server=/netflixdnstest4.com/4.4.4.4
server=/netflixdnstest5.com/4.4.4.4
server=/netflixdnstest6.com/4.4.4.4
server=/netflixdnstest7.com/4.4.4.4
server=/netflixdnstest8.com/4.4.4.4
server=/netflixdnstest9.com/4.4.4.4
server=/netflixdnstest10.com/4.4.4.4
server=/netflixinvestor.com/4.4.4.4
server=/netflixtechblog.com/4.4.4.4
server=/akadns.net/4.4.4.4
server=/akam.net/4.4.4.4
server=/akamai.com/4.4.4.4
server=/akamai.net/4.4.4.4
server=/akamaiedge.net/4.4.4.4
server=/akamaihd.net/4.4.4.4
server=/akamaistream.net/4.4.4.4
server=/akamaitech.net/4.4.4.4
server=/akamaitechnologies.com/4.4.4.4
server=/akamaitechnologies.fr/4.4.4.4
server=/akamaized.net/4.4.4.4
server=/disney.asia/4.4.4.4
server=/disney.be/4.4.4.4
server=/disney.bg/4.4.4.4
server=/disney.ca/4.4.4.4
server=/disney.ch/4.4.4.4
server=/disney.co.il/4.4.4.4
server=/disney.co.jp/4.4.4.4
server=/disney.co.kr/4.4.4.4
server=/disney.co.th/4.4.4.4
server=/disney.co.uk/4.4.4.4
server=/disney.co.za/4.4.4.4
server=/disney.com/4.4.4.4
server=/disney.com.au/4.4.4.4
server=/disney.com.br/4.4.4.4
server=/disney.com.hk/4.4.4.4
server=/disney.com.tw/4.4.4.4
server=/disney.cz/4.4.4.4
server=/disney.de/4.4.4.4
server=/disney.dk/4.4.4.4
server=/disney.es/4.4.4.4
server=/disney.fi/4.4.4.4
server=/disney.fr/4.4.4.4
server=/disney.gr/4.4.4.4
server=/disney.hu/4.4.4.4
server=/disney.id/4.4.4.4
server=/disney.in/4.4.4.4
server=/disney.io/4.4.4.4
server=/disney.it/4.4.4.4
server=/disney.my/4.4.4.4
server=/disney.nl/4.4.4.4
server=/disney.no/4.4.4.4
server=/disney.ph/4.4.4.4
server=/disney.pl/4.4.4.4
server=/disney.pt/4.4.4.4
server=/disney.ro/4.4.4.4
server=/disney.ru/4.4.4.4
server=/disney.se/4.4.4.4
server=/disney.sg/4.4.4.4
server=/20thcenturystudios.com.au/4.4.4.4
server=/20thcenturystudios.com.br/4.4.4.4
server=/20thcenturystudios.jp/4.4.4.4
server=/adventuresbydisney.com/4.4.4.4
server=/babble.com/4.4.4.4
server=/babyzone.com/4.4.4.4
server=/bamgrid.com/4.4.4.4
server=/beautyandthebeastmusical.co.uk/4.4.4.4
server=/dilcdn.com/4.4.4.4
server=/disney-asia.com/4.4.4.4
server=/disney-discount.com/4.4.4.4
server=/disney-plus.net/4.4.4.4
server=/disney-portal.my.onetrust.com/4.4.4.4
server=/disney-studio.com/4.4.4.4
server=/disney-studio.net/4.4.4.4
server=/disney.my.sentry.io/4.4.4.4
server=/disneyadsales.com/4.4.4.4
server=/disneyarena.com/4.4.4.4
server=/disneyaulani.com/4.4.4.4
server=/disneybaby.com/4.4.4.4
server=/disneycareers.com/4.4.4.4
server=/disneychannelonstage.com/4.4.4.4
server=/disneychannelroadtrip.com/4.4.4.4
server=/disneycruisebrasil.com/4.4.4.4
server=/disneyenconcert.com/4.4.4.4
server=/disneyiejobs.com/4.4.4.4
server=/disneyinflight.com/4.4.4.4
server=/disneyinternational.com/4.4.4.4
server=/disneyinternationalhd.com/4.4.4.4
server=/disneyjunior.com/4.4.4.4
server=/disneyjuniortreataday.com/4.4.4.4
server=/disneylatino.com/4.4.4.4
server=/disneymagicmoments.co.il/4.4.4.4
server=/disneymagicmoments.co.uk/4.4.4.4
server=/disneymagicmoments.co.za/4.4.4.4
server=/disneymagicmoments.de/4.4.4.4
server=/disneymagicmoments.es/4.4.4.4
server=/disneymagicmoments.fr/4.4.4.4
server=/disneymagicmoments.gen.tr/4.4.4.4
server=/disneymagicmoments.gr/4.4.4.4
server=/disneymagicmoments.it/4.4.4.4
server=/disneymagicmoments.pl/4.4.4.4
server=/disneymagicmomentsme.com/4.4.4.4
server=/disneyme.com/4.4.4.4
server=/disneymeetingsandevents.com/4.4.4.4
server=/disneymovieinsiders.com/4.4.4.4
server=/disneymusicpromotion.com/4.4.4.4
server=/disneynewseries.com/4.4.4.4
server=/disneynow.com/4.4.4.4
server=/disneypeoplesurveys.com/4.4.4.4
server=/disneyplus.bn5x.net/4.4.4.4
server=/disneyplus.com/4.4.4.4
server=/disneyplus.com.ssl.sc.omtrdc.net/4.4.4.4
server=/disneyredirects.com/4.4.4.4
server=/disneysrivieraresort.com/4.4.4.4
server=/disneystore.com/4.4.4.4
server=/disneystreaming.com/4.4.4.4
server=/disneysubscription.com/4.4.4.4
server=/disneytickets.co.uk/4.4.4.4
server=/disneyturkiye.com.tr/4.4.4.4
server=/disneytvajobs.com/4.4.4.4
server=/disneyworld-go.com/4.4.4.4
server=/dssott.com/4.4.4.4
server=/go-disneyworldgo.com/4.4.4.4
server=/go.com/4.4.4.4
server=/mickey.tv/4.4.4.4
server=/moviesanywhere.com/4.4.4.4
server=/nomadlandmovie.ch/4.4.4.4
server=/playmation.com/4.4.4.4
server=/shopdisney.com/4.4.4.4
server=/shops-disney.com/4.4.4.4
server=/sorcerersarena.com/4.4.4.4
server=/spaindisney.com/4.4.4.4
server=/star-brasil.com/4.4.4.4
server=/star-latam.com/4.4.4.4
server=/starwars.com/4.4.4.4
server=/starwarsgalacticstarcruiser.com/4.4.4.4
server=/starwarskids.com/4.4.4.4
server=/streamingdisney.net/4.4.4.4
server=/thestationbymaker.com/4.4.4.4
server=/thisispolaris.com/4.4.4.4
server=/watchdisneyfe.com/4.4.4.4

resolv-file=/etc/resolv.dnsmasq.conf

```

2.配置文件/etc/resolv.dnsmasq.conf

```shell
vi /etc/resolv.dnsmasq.conf
```

```conf
nameserver 1.1.1.1
nameserver 8.8.8.8
```

3.设置 VPS 系统 DNS，将本机 dnsmasq 作为系统 DNS 服务器。参考上面的操作

```shell
chattr -i /etc/resolv.conf
echo -e "nameserver 127.0.0.1" > /etc/resolv.conf
chattr +i /etc/resolv.conf
```

4.重启 dnsmasq

```shell
/etc/init.d/dnsmasq restart
# 或者
systemctl restart dnsmasq
```

四.卸载

```shell
apt-get remove dnsmasq
mv /etc/resolv.conf.link /etc/resolv.conf
chattr -i /etc/resolv.conf
chmod 777 /etc/resolv.conf
# echo -e "nameserver 8.8.8.8" > /etc/resolv.conf
sudo systemctl enable systemd-resolved --now
```
