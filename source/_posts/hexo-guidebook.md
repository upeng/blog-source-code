---
title: hexo+github搭建blog
date: 2016-04-20 21:31:48
tags: hexo
categories: 其他
---

hexo是一个快速、简介且高效的博客框架！

## 安装前置服务
hexo依赖nodejs和git 

* 由于依赖nodejs，因此安装hexo之前需要先安装nodejs
* 使用github搭建静态博客，因此需要安装git客户端（使用hexo命令编译并上传至github）

> [百度云盘下载链接][1]
  [1]: http://pan.baidu.com/s/1dENChzF

## 快速创建博客页面

### hexo server 
```
//启动本地服务，查看博客主页
$ hexo server
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```
### hexo new "my new post"

```
//创建文章页
$ hexo new "hexo guidebook"
INFO  Created: g:\hexo\source\_posts\hexo-guidebook.md
```
### hexo new page "categories"

> 创建单页面，比如分类，关于

### hexo g (hexo generate) 

>生成静态页面

### hexo deploy （上传至github）

> 如果没有建立github与本地git的关联，会报错，因为本地和github没有建立信任机制是不能随意上传至github的


## 补充：如何建立信任机制

### 生成新的Key：
```
ssh-keygen -t rsa -C "your_email@youremail.com"  //（引号内的内容替换为你自己的邮箱）
```

* 输出显示

> Generating public/private rsa key pair. Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):

* 直接回车，不要修改默认路劲。

```
Enter passphrase (empty for no passphrase): 
Enter same passphrase again:
```
* 设置一个密码短语，在每次远程操作之前会要求输入密码短语！闲麻烦可以直接回车，不设置。成功：

> Your identification has been saved in /Users/your_user_directory/.ssh/id_rsa. Your public key has been saved in /Users/your_user_directory/.ssh/id_rsa.pub. The key fingerprint is: ... ...

### 提交公钥：

* 找到.ssh文件夹，用文本编辑器打开“id_rsa.pub”文件，复制内容到剪贴板。

*  打开 https://github.com/settings/ssh ，点击 Add SSH Key 按钮，粘贴进去保存即可。

## 踩到的坑
hexo deploy后报一堆错误如下，在初次使用git命令也许会遇到
```
warning: LF will be replaced by CRLF in .....
```
> 有效解决方法：

1. 在git bash  输入下面的命令：
```
git config --global core.autocrlf  false
```
2. 删掉项目中的.git文件夹 `rm -rf .git`

3. 重新
git init 
hexo deploy
问题解决！


参考【其他一些主题配置文章】
http://jianghao.wang/2015/hexo-github/
http://zhiho.github.io/2015/09/29/hexo-next/