title: Mac下安装LNMP(Nginx+PHP5.6)环境
tags: Mac
categories: 其他
date: 2016-08-28 22:28:49
---
## 安装Homebrew
从零开始安装Mac下LNMP环境的过程

> 确保系统已经安装xcode，然后使用一行命令安装依赖管理工具Homebrew

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

之后就可以使用
```
brew install FORMULA
```
来安装所需要的依赖了.更详细的信息参考Homebrew的[官方Cookbook][1]

因此使用Homebrew常见的流程是：
1. 增加一个程序源（新增一个水龙头） brew tap homebrew/php
2. 更新程序源 brew update
3. 安装程序包（按照配方酿酒） brew install git
4. 查看配置 brew config 可以看到程序包默认安装在/usr/local/Cellar下 （酒桶放在地窖内）
<!-- more -->
## 安装PHP5.6（FPM方式）
首先加入Homebrew官方的几个软件源
```
brew tap homebrew/dupes
brew tap homebrew/versions
brew tap homebrew/php
```

PHP如果采用默认配置安装，会编译mod_php模块并只运行在Apache环境下，为了使用Nginx，这里需要编译php-fpm并且禁用apache，主要通过参数`--without-fpm --without-apache`来实现。完整的安装指令为
```
brew install php56 \
--without-snmp \
--without-apache \
--with-debug \
--with-fpm \
--with-intl \
--with-homebrew-curl \
--with-homebrew-libxslt \
--with-homebrew-openssl \
--with-imap \
--with-mysql \
--with-tidy
```
由于OSX已经自带了PHP5.5环境，因此需要修改系统路径，优先运行brew安装的版本，在`~/.bashrc`里加入：
```
export PATH="/usr/local/bin:/usr/local/sbin:$PATH"
```

> 这时运行 php -v查看版本是否是5.6
运行php -m 查看PHP安装都扩展列表

如果要安装新的php扩展，可以直接安装而不用每次重新编译php，所有的扩展可以通过
```
brew search php56
```
看到，下面是我自己所需要的扩展，可以支持Phalcon框架：
```
brew install php56-gearman php56-msgpack php56-memcache php56-memcached php56-mongo  php56-phalcon php56-redis php56-xdebug
```
### PHP-FPM的加载与启动

php-fpm.conf  php.ini
```
/usr/local/etc/php/5.6
```
php-fpm进程
```
/usr/local/sbin
```
**安装完毕后可以通过以下指令启动和停止php-fpm**
```
./php-fpm -D    //进入sbin目录

killall php-fpm
```
同时可以将php-fpm加入开机启动
```
ln -sfv /usr/local/opt/php56/*.plist ~/Library/LaunchAgents

launchctl load ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
```
### 查看php-fpm进程
查看9000端口
`lsof -i:9000`
或者
`ps -ef | grep php-fpm`
## 安装Nginx
```
brew install nginx
```
安装完毕后可以通过
```
nginx
nginx -s quit
```
启动和关闭，同时也支持重载配置文件等操作
```
nginx -s reload|reopen|stop|quit
```
nginx安装后默认监听8080端口，可以访问http://localhost:8080 查看状态。如果要想监听80端口需要root权限，运行

```
sudo chown root:wheel /usr/local/Cellar/nginx/1.6.2/bin/nginx
sudo chmod u+s /usr/local/Cellar/nginx/1.6.2/bin/nginx
```

> 我是直接在nginx配置中改成80端口，然后重启生效
并使用root权限启动

```
sudo nginx
```
开机启动
```
ln -sfv /usr/local/opt/nginx/*.plist ~/Library/LaunchAgents

launchctl load ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist
```

## Nginx + PHP-FPM配置
好文：http://www.nginx.cn/nginx-how-to

Nginx一般都会运行多个域名（虚拟主机）
> 按Ubuntu的文件夹结构来存放Nginx的配置文件

```
mkdir -p /usr/local/var/logs/nginx
mkdir -p /usr/local/etc/nginx/sites-available
mkdir -p /usr/local/etc/nginx/sites-enabled
mkdir -p /usr/local/etc/nginx/conf.d
mkdir -p /usr/local/etc/nginx/ssl
```
> 联想到work中，vhost和fpm.d目录，新增域名与目录时，只需要增加两个配置文件；前提时php-fpm.conf和nginx.conf主配置文件中都有

```
include vhost/*.conf
include fpm.d/*.conf
```
**上述都conf.d等同于fpm.d，sites-enabled等同于vhost**

### 编辑Nginx全局配置
```
vim /usr/local/etc/nginx/nginx.conf
```
```
worker_processes  1;
error_log   /usr/local/var/logs/nginx/error.log debug;
pid        /usr/local/var/run/nginx.pid;

events {
    worker_connections  256;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] '
        '"$request" $status $body_bytes_sent '
        '"$http_referer" "$http_user_agent" '
        '"$http_x_forwarded_for" $host $request_time $upstream_response_time $scheme '
        '$cookie_evalogin';

    access_log  /usr/local/var/logs/access.log  main;

    sendfile        on;
    keepalive_timeout  65;
    port_in_redirect off;

    include /usr/local/etc/nginx/sites-enabled/*;
}
```
这样一来首先可以把一些可复用配置独立出来放在`/usr/local/etc/nginx/conf.d`下，比如fastcgi的设置就可以独立出来
```
vim /usr/local/etc/nginx/conf.d/php-fpm
```
内容为
```
location ~ \.php$ {
    try_files                   $uri = 404;
    fastcgi_pass                127.0.0.1:9000;
    fastcgi_index               index.php;
    fastcgi_intercept_errors    on;
    include /usr/local/etc/nginx/fastcgi.conf;
}
```
然后`/usr/local/etc/nginx/sites-enabled`目录下可以一个文件对应一个域名的配置，比如web服务器目录是`/opt/htdocs`
```
vim /usr/local/etc/nginx/sites-enabled/default
```
```
server {
    listen       80;
    server_name  localhost;
    root         /opt/htdocs/;

    location / {
        index  index.html index.htm index.php;
        include     /usr/local/etc/nginx/conf.d/php-fpm;
    }
}
```
此时启动了php-fpm并且启动了Nginx后，就可以通过http://localhost来运行php程序了

## 安装MySQL
```
brew install mysql
```
可以通过
```
mysql.server start
mysql.server stop
```

来启动／停止，启动后默认应为空密码，可以通过mysqladmin设置一个密码
```
mysqladmin -uroot password "mypassword"
```

但是在操作的时候出现了空密码无法登入的情况，最终只能通过mysqld_safe来设置
```
sudo mysqld_safe --skip-grant-tables
mysql -u root
mysql> UPDATE mysql.user SET Password=PASSWORD('mypassword') WHERE User='root';
mysql> FLUSH PRIVILEGES;
```
**依旧报错**
> **坑1**：一直出现mysql.sock错误，`mysql -uroot -p`;
**填坑1**：`mysql -h 127.0.0.1 -u root -p`  why?

> **坑2**：设置密码报错：`Password`字段不存在
**填坑2**：mysql5.7已将`Password`字段去掉，改成`authentication_string`；所以正确都姿势如下：
```
update mysql.user set authentication_string=password('123qwe') where user='root
```
> 老版本mysql没变

最后将MySQL加入开机启动
```
cp /usr/local/Cellar/mysql/5.6.22/homebrew.mxcl.mysql.plist ~/Library/LaunchAgents/
```
## 查看服务列表
```
brew services list
```
```
Name  Status  User    Plist
mysql started root    /Library/LaunchDaemons/homebrew.mxcl.mysql.plist
nginx started yupeng6 /Users/yupeng6/Library/LaunchAgents/homebrew.mxcl.nginx.plist
php56 started yupeng6 /Users/yupeng6/Library/LaunchAgents/homebrew.mxcl.php56.plist
```

安装redis和memcache过程省略，详见下面博文，本文也是参考此
http://avnpc.com/pages/install-lnmp-on-osx