Linux 的版本集成了 Unix 的版本定制规则，分为内核版本和发行版本

内核版本：内核就是一个核心，其他的软件都基于这个核心，不能直接使用，内核版本统一在 http：、、www.linux.org 发布

发行版本：由各个 Linux 发行商发布，Linux 发行商有权选择 Linux 的内核版本。常见的 Linux 发行版本：Redhat、centos、debian、Ubuntu

内核版本分为稳定版和开发版，区分方式是根据次版本的奇偶判定，技术为开发版本，偶数为稳定版

- debian

  运行起来极其稳定，它非常适合用于服务器

- Redhat

  第一款面向商业市场的 Linux 发行版本，有服务器版本，支持众多处理器架构

  全球最大的 Linux 发行厂商，功能全面，稳定

- Ubuntu

  Ubuntu 是 Debian 的一款衍生版，侧重于它在这个市场的应用，在服务器、云计算、甚至一些运行的移动设备上很常见

- centos

  centos 是一款企业级 Linux 发行版，它使用红帽企业级 Linux 中的免费源代码重新构建而成。这款重构版完全去掉了注册商标以及 binary 程序

- Fedora

  有庞大的用户，软件包多，同样使用 yum 来管理软件包

配置网络，其实就是虚拟机的配置，现在虚拟机也仅用于学习我就不贴了

## 基本命令

df 统计磁盘空间或文件系统使用情况

cp 复制 mv 剪贴

cat 输出文件的全部，仅适合很小的文件

more less 查看文件的一部分，然后去翻页或者一行一行的查看

less  `/要搜索的字符串` n下一个 N上一个

head 输出文件的开始的一部分 `head -n 3` 输出文件前三行

tail 输出文件的最后一部分，`tail -f 文件名` 输出文件的最后，并实时更新

定向输出 `>` 覆盖后面的文件，`>>` 追加到后面的文件中

管道 `|` 讲一个命令的输出作为另一个命令的输入

逻辑控制 `&&` 左边的命令运行成功才会继续运行右边的指令

### 压缩

```shell
tar -cvf 只打包不压缩
# 压缩文件
tar -zcvf 将要打包压缩的文件名.tar.gz 被压缩的文件/目录

# 解压缩文件
tar -zxvf 打包文件.tar.gz [-C 解压缩到某个目录路径]

# bzip2 格式
tar -jcvf 将要打包压缩的文件名.tar.bz2 
tar -jxvf 需要解压缩文件.tar.bz2 
```

bzip2 格式可能没有，需要安装 `yum install -y bzip2`

### 时间日期

date 命令查看时间

```shell
(1)date (功能描述:显示当前时间)
(2)date +%Y (功能描述:显示当前年份)
(3)date +%m (功能描述:显示当前月份)
(4)date +%d (功能描述:显示当前是哪一天)
(5)date +%Y%m%d ... (功能描述:显示当前年月日各种格式 )
(6)date "+%Y-%m-%d %H:%M:%S" 或者单引号也可以 (功能描述:显示年月日时分秒)

# 非当前时间
(1)date -d '1 days ago' (功能描述:显示前一天日期) 
(2)date -d yesterday +"%Y-%m-%d"(同上)
(3)date -d next-day +"%Y-%m-%d" (功能描述:显示明天日期) 
(4)date -d 'next monday' (功能描述:显示下周一时间)
```

cal 日历

### 查找

find 查找文件或者目录

```shell
find /etc -name yum.conf #在/etc目录下文件yum.conf
find /etc -name 'yum' #使用通配符*(0或者任意多个)。表示在/etc目录下查找文件名中含有 字符串‘yum’的文件
find . -name 'yum*' #表示当前目录下查找文件名开头是字符串‘yum’的文 件
```

特征查找

```
find / -atime -2 # 查找在系统中最后48小时访问的文件 (Access Time，文件读取访问时间)
find / -empty # 查找在系统中为空的文件或者文件夹
find / -group susan # 查找在系统中属于group为susan的文件
find / -mtime -1 #查找在系统中最后24小时里修改过的文件 (modify time)
find / -user susan #查找在系统中属于susan这个用户的文件
find / -size +10000c #查找出大于10000字节的文件(c:字节，w:双字，k:KB，M:MB，G:GB)
find / -size -1000k #查找出小于1000KB的文件





```

使用混合查找方式查找文件 参数有: `!，-and(-),-or(-o)`

```shell
find /tmp -size +10c -and -mtime +2 #在/tmp目录下查找大于10字节并在2天前修改的文件 
find / -user root -or -user susan #在/目录下查找用户是root或者susan用户的文件 
find /tmp ! -user susan #在/tmp目录中查找所有不属于susan用户的文件
```

grep 过滤查找，grep是根据**文件的内容进行**查找，会对文件的每一行按照给定的模式(pattern)进行匹配查找。

##  
