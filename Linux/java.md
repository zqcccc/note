
## java 安装

java 环境变量配置 `/etc/profile` 的末尾：

JAVA_HOME 的路径是不一定的，这边只是举个例子是这样的，具体也看你是真的安装到哪里去了

```bash
export JAVA_HOME=/usr/java/jdk1.8.0_141/
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin
```

使配置生效

```shell
source /etc/profile
java -version
```

能看到 Java 的版本就是正确的

## tomcat 安装

```shell
tar -zxvf apache-tomcat-7.0.57.tar.gz -C /usr/tomcat
/usr/tomcat/bin/startup.sh && tail -200f /usr/tomcat/logs/catalina.out
```

然后访问 ip:8080 就有 Tomcat 的页面了

`cd /usr/tomcat/bin && ./shutdown.sh` 就能关闭了 tomcat 了

## mysql 安装

先查看系统里是否安装了 mysql

```shell
rpm -qa | grep mysql
# mysql-libs-5.1.73-8.el6_8.x86_64
```

如果有结果的话就可能需要去卸载（前面的 Java 安装、tomcat 安装也是需要的）

`rpm -e --nodeps mysql-libs-5.1.73-8.el6_8.x86_64`

下载安装官网 yum 源

```shell
ll /etc/yum.repos.d/

wget -P /usr/software http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
```

进到 `/usr/software` 目录里去安装下载好的 rpm 文件

```shell
rpm -ivh mysql-community-release-el6-5.noarch.rpm
```

这样我们的 yum 源就有了 mysql 了，然后通过 yum 来安装 mysql，前面安装 java 和 tomcat 都是直接去官网下载包来安装的，没有用 yum

```shell
yum -y install mysql-community-server
```

安装好了就可以启动 mysql 了 `service mysqld start`

```shell
/usr/bin/mysqladmin -u root password '123'

mysql -uroot -p123
```

由于 mysql 编码问题会出现数据库出现乱码

解决方法是修改 mysql 数据库字符编码为 UTF-8，UTF-8 包含全世界所有国家需要用到的字符，是国际编码

```mysql
show variables like 'character_set_%';
```

```
>/etc/my.cnf # 清空 my.cnf 文件内容
vim /etc/my.cnf
```

/etc/my.cnf 文件改成

```
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
character-set-server=utf8
```

```shell
service mysqld restart
```

重启 Mysql 后再去数据库理查询字符集的时候，拉丁字符集就会变成 utf-8 了

### mysql 远程连接

默认情况下，使用客户端远程连接 mysql 是报错的

先进入 mysql 数据库

```mysql
show databases;
use mysql;
show tables;
select host,user from user;
```

user 表就是来控制哪些用户可以连接是用数据库的

```mysql
# 给 root 授权，既可以本地访问，也可以远程访问
grant all privileges on *.* to 'root'@'%' identified by '123' with grant option;

# 刷新权限
flush privileges;

# 再次查看 user 表应该多了一条记录了
select host,user from user;
```