---
title: shell脚本由点到面学习总结
tags:
  - shell
  - linux
categories:
  - Linux
author: tayloryu
date: 2017-09-27 22:38:00
---

以前只是零零散散写过一些简单的命令行，比如awk、sed、nc、ls、comm这些简单的命令，几乎没有写过一个完整的shell脚本，像循环、条件判断、数据库操作等几乎都没怎么写过，总觉得shell写起来太麻烦，要学习的东西太多了，其实主要是懒。这次由于业务需要，使用shell脚本能方便解决问题，便硬着头皮上了，借着这个机会把常用的命令整理一下，算作温习吧。

## 实现功能：
将下面未经处理的原始数据，批量导入到es，并记录导入的数据源；
```
 20170101        ABCD-EF-G001-12345678    1rt2xete3   CD      1.1977199809152
 20170101        ABCD-EF-G001-12345678    1rt2xete4   CD      5.856237342947999
 20170101        ABCD-EF-G001-12345678    1rt2xete5   CD      5.1901199172992
 20170101        ABCD-EF-G001-12345678    1rt2xete6   CD      0.748574988072
 20170101        ABCD-EF-G001-12345678    1rt2xete7   CD      2.4472449769392
 20170101        ABCD-EF-G001-12345678    1rt2xete8   CD      9.62620145261278
 20170101        ABCD-EF-G001-12345678    1rt2xete9   CD      0.1249524996024
```
<!-- more -->
## 问题说明：
* 数据量很大，从1月1号到9月20号，每天一个文件，每个文件大概100w行（约100M），导入es通过http请求，虽然es支持批量bulk导入，但是批量数据大小是有限度的，超过大小直接kill掉

## 要求
* 导入到es的数据源需要完整记录备份，作为日志，如果导入失败可以补录。
* 虽然数据源每天一个文件堆在一起，但是日志却不能每个文件都放在一个文件夹里，所以需要设计成按年月（如201701,201702）分为一级文件夹，然后按天（如01,02...30）分为二级文件夹,每个文件夹存储当天的源文件，切割文件等信息

## 步骤
**step1：** 获取日期参数，格式Ymd，如果不传则默认取当天日期

**step2：** 根据日期获取源文件（源文件格式`年月日.txt`）

**step3：** 定义输出文件目录格式

**step4：** 定义es数据源文件

**step5：** 将源文件格式化成es数据原文件，用到sed命令

**step6：** 将es数据源文件进行切割，用到split命令

**step7：** 获取切割后的文件组成数组

**step8：** 循环调用es接口批量入数据

## 子脚本剖析


> step1

1）条件判断 if [condition]; then command1 else command2 fi
2）获取命令行参数 $，$0，$1...
3）复杂条件判断，多个if else;switch case 
 详细【待总结】

自定义日期格式
如：`date "+%Y-%m-%d %H:%M:%S"`详细【待总结】 参考：
 http://blog.csdn.net/shanliangliuxing/article/details/16821175
 http://blog.csdn.net/jk110333/article/details/8590746

```
if [ $# -eq 0 ]; then 
	DATE=`date +%Y%m%d`
else
	DATE=$1
fi
```

> step2

```
source_file_path=/data/codes
source_file=$source_file_path/$DATE.txt
```

> step3

定义年月、日目录名称, 递归创建多层文件目录
判断文件或目录是否存在，`（-d -f）`
详细【待总结】
```
folderYearMonth=`date -d $DATE +"%Y%m"`
folderDay=`date -d $DATE +"%d"`
output_file_path=/data/home/upeng/$folderYearMonth/$folderDay
if [ ! -d "$output_file_path" ]; then
	mkdir -p "$output_file_path"
fi
```

> step4

```
es_source_file=$output_file_path/$DATE.json
start_at=$(date +%s)  //转换成时间戳
```
> step5

`sed`命令总结；多个`sed`同时编辑，理解步骤先后顺序，详细【待总结】
```
sed 's/\t/,/g;
s/^/{"date":"&/g;
s/,/","code":"/1;
s/,/","oid":"/2;
s/,/","type":"/3;
s/,/","salary":"/4;s/$/&"}/g;
s/^/{"index":{}}\n&/g' $source_file > $es_source_file
```

> step6

`split` 文件切割，详细【待总结】
```
split -l 500000 $es_source_file $output_file_path/$date_
```
> step7

`awk`、数组，详细【待总结】
```
arrs=($(ls -l $output_file_path/$date_* | awk '{print $9}'))
```

> step8

循环调用`es`接口批量入数据，详细【待总结】
```
for arr in ${arrs[@]}
do
	curl -XPOST 'http://127.0.0.1:9200/media/order/_bulk?pretty' --data-binary @$arr
done

end_at=$(date +%s)
echo "it takes $((end_at-start_at)) seconds"

```

## 父脚本剖析
```
source_file_path=/data/codes
script_path=/data/home/upeng
cd $source_file_path
DATES=($(ls -l *".txt" | awk '{print $9}' | awk -F '.' '{print $1}'))

for date in ${DATES[@]}
do
	folderYm=`date -d $DATES +"%Y%m"`
	folderD=`date -d $DATES +"%d"`
	output_file_path=$script_path/$folderYm/$folderD
	if [ ! -d "$output_file_path" ]; then
		sh $script_path/child_script.sh $date
		cat /dev/null > $script_path/nohup.out
	fi
done

```

1. 上述脚本中`待总结`的部分是每个点需要完善的面
2. 上述脚本还有诸多待完善部分，当前只是能够运行，异常情况如何处理如何捕获待整理