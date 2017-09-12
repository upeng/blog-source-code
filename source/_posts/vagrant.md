---
title: vagrant-快速搭建linux开发环境
date: 2016-07-13 23:32:54
tags: vagrant
categories: 其他
---


平时开发测试使用公司提供的弹性开发机（Centos），自己想折腾一些东西又不方面在上面部署,鉴于

* 学习linux考虑（比如经常装扩展插件啥的），装WAMP有些low而且不适合扩展。
* 在windows上装linux虚拟机（VMare等）又太占内存（o(╯□╰)o我的笔记本只有4G内存。。。）

有没有那种快捷的搭建linux开发环境的东东呢？答案是肯定的。偶然间发现了vagrant，它就像一个容器一样（Docker？），部署LAMP十分快捷；
<!-- more -->
## 我所理解的vagrant box
我理解的，vagrant box就是一个简易linux操作系统内核，到vagrant官网可以看到很多【http://www.vagrantbox.es/】， 选择一个box下载下来，然后通过vagrant add box就可以直接初始化一个linux系统了；如果你想创建多个linux系统，那就多add 几个，所以vagrant也可以练习分布式！但是，这个网站的速度也慢的可以，推荐一个国内镜像，还是挺快的，每秒20M左右 【http://mirrors.hypo.cn/ubuntu-vagrant/】

## 预备软件
virtualBox : 运行linux的环境 【https://www.vagrantup.com/】
vagrant：提供一些vagrant命令 【https://www.virtualbox.org/】
百度云盘备份：【http://pan.baidu.com/s/1o8aoofo】

## vagrant box下载
记得第一次是“在线”下载vagrant box，执行vagrant init；vagrant up之后，当检测到无box时会自动下载；速度慢的惊人（虽然我开了vpn），而且在线安装的box直接就在我们vagrant up命令中执行了，没有见到box后缀的文件，当你重装系统后或者你安装的vagrant损坏后，难道又要重新在线安装box？ +_+ 所以建议下载box文件，然后使用vagrant add box命令


## vagrant 常用命令(快速搭建LAMP流程)
### 添加box
vargant box add  trusty  trusty-server-cloudimg-amd64-vagrant-disk1.box
// vagrant box add  box默认名称是base,此处我们定义为trusty  .box文件就是我们下载的box

### 初始化
vagrant init
因为我们重新命名了我们的box为trusty，所以你再任何地方只要执行vagrant init，生成的Vagrant文件中config.vm.box='trusty'；
如果执行vagrant up会失败，因为vagrant init默认以base为名初始化；正确的初始化姿势是
vagrant init trusty

### 启动虚拟机
vargant up
vagrant up之前需要将Vagrant文件的第25行注释去掉，这样才能在windows中访问我们在linux中创建的程序；在trusty版本的Ubuntu中，有个目录 `/vagrant`与本地windows中Vagrant同级，因此可以本地编辑，linux中运行，就像磁盘映射。


### 登录虚拟机
vagrant ssh
使用vagrant ssh进入Ubuntu系统；进入后需要更新升级Ubuntu的一些包和工具，同事需要安装我们的LAMP。
升级包命令： `sudo apt-get update`
安装php apache: `sudo apt-get install apache2 php5 libapache2-mod-php5`
安装mysql: `sudo apt-get install mysql-server mysql-client php5-mysql`

### 配置apache2
安装完成后访问：`http://localhost:8080` , 默认指向 `/var/www/html` ,修改配置文件如下：
配置文件路径  `/etc/apache2/sites-available/000-default.conf`

```
DocumentRoot /vagrant
<Directory /vagrant>
                Options -Indexes +followSymLinks +MultiViews
                AllowOverride all
                Require all granted
</Directory>
```
修改完成后重启apache `service apache2 restart`

参考：
http://rrylee.github.io/2015/11/26/quick-start-linux-php/
http://rmingwang.com/vagrant-commands-and-config.html