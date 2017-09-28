---
title: linux常用命令之sed
date: 2017-09-28 22:23:10
tags:
  - sed
  - linux
categories:
  - Linux
---


## sed工作原理：

sed在正常情况下，将处理的行读入模式空间，脚本中的sed命令就一条接着一条进行处理，直到脚本执行完毕。然后该行被输出，模式空间被清空；接着，在重复执行刚才的动作，文件中的新的一行被读入，直到文件处理完毕。【读入的每一行删除行尾的换行符放入模式空间，再执行所有命令，执行完所有的命令之后，再加回删过的换行符并打印到输出】

## sed替换操作（s命令）

* 使用后缀 `/g` 标记会替换每一行中的所有匹配, `/n`表示替换每一行第n处匹配
* `sed -i` 表示在源文件基础上修改

<!-- more -->
`sed 's/^/str&/g' filename`  			//每行前加上字符串str
`sed 's/$/&str/g' filename`   			//每行末尾加上字符串str

`sed 's/oldstr/newstr/g' filename`  	//字符串替换

`sed 's/str/,str/g' filename`   		//str前加上逗号，本质上还是字符串替换
`sed 's/str//g' filename`   			//删除字符串str，本质上还是字符串替换
`sed 's/str.*//g' filename`  			//删除字符串str之后的所有字符（包括str）

`sed 's/\s\+/,/g' filename` 	//替换一个或多个空格为逗号，其中\s代表空格，+代表出现一次或多次。
`sed 's/\t/,/g'	filename`		//替换tab为逗号

`s/^/hello\n&/g filename`		//每一行前面加上一行hello

## 组合多个表达式(多个sed命令)

一定要注意顺序，因为多个命令前后是有依赖的，每一个命令都是在前一个命令执行完成的基础上进行的，数据自然也是前一个命令处理后的数据。
**使用方法：**
1. `sed '表达式' | sed '表达式'`
2. `sed '表达式; 表达式'`


## sed的其他命令参考

http://man.linuxde.net/sed