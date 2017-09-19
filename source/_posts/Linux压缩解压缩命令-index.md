title: Linux常用压缩解压缩命令
tags: [linux]
categories: [Linux]
date: 2017-09-10 21:00:00
---
**tar后缀文件**
//解包
`tar xvf FileName.tar`
//打包
`tar cvf FileName.tar DirName`

**zip后缀文件**
//解压
`unzip FileName.zip`
//压缩
`zip FileName.zip DirName`
压缩一个目录使用 -r 参数，-r 递归。例： `$ zip -r FileName.zip DirName`

<!-- more -->
**tar.gz 和 .tgz后缀文件**
//解压
`tar zxvf FileName.tar.gz`
//压缩
`tar zcvf FileName.tar.gz DirName`

**rar后缀文件**
//解压
`rar x FileName.rar`
//压缩
`rar a FileName.rar DirName`
rar请到：`http://www.rarsoft.com/download.htm` 下载！
解压后请将`rar_static`拷贝到`/usr/bin`目录（其他由$PATH环境变量指定的目录也可以）：
`[root@www]# cp rar_static /usr/bin/rar`　　
