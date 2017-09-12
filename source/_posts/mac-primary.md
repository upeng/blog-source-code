---
title: 首次折腾Mac
date: 2016-08-28 10:09:29
tags: Mac
categories: 其他
---

### 常用快捷键

* delete（backspac）
* fn＋delete（后删）
* option＋v ＝ √
* command ＋ space ＝ spotlight
* option ＋ space ＝ hotkey window 呼起item2
* control ＋ space ＝ 输入法切换

### 开发配置
查看**Mac开发配置手册**省了很多时间
https://aaaaaashu.gitbooks.io/mac-dev-setup/content/
iTem2文档也是相当之多
https://leohxj.gitbooks.io/a-programmer-prepares/content/software/mac/softwares/iTerm2.html
<!-- more -->
> **坑**：在App Store中安装xcode，总是提示第一次使用，需要输入一堆东西，然而没有什么卵用，浪费了我好多时间。果断去`https://developer.apple.com/xcode/`下载,下载Xcode command line tools即可，只有几百M.

#### 安装homebrew
mac下的包管理工具，类似于ubuntu的apt-get
brew install nginx ＝ apt-get install nginx

#### 安装iTem2

> **坑**：字体乱码
安装字体即可：monaco for powerline下载安装完成后在设置中设为该字体即可

* hotkey window  	//option ＋ space启动
* default   		//dock中的快速启动
二者均可在`profiles/open profies/edit profiles`; 设置`color，text`等等


### hostname
`hostname`  //查看hostname
`sudo scutil --set HostName MacBook-pro`   //修改hostname


#### 安装开发环境LNMP
鼓捣了好久，最后还是按照下面这个教程来操作了，从零开始安装Mac下的LNMP环境的过程