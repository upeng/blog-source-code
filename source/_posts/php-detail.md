---
title: 几个值得深思的 PHP 面试问题
date: 2016-05-28 00:04:26
tags: php
categories: PHP
---

5个值得深思的 PHP 面试问题
文章所罗列的问题虽然看似简单，但是每个背后都涵盖了一个或几个大家容易忽视的基础知识点，希望能够帮助到你的面试和平时工作。
<!-- more -->
**Q1**
```
$str1 = 'hello world';
$str2 = 'he';
if (strpos($str1, $str2))
{
    echo "/"" . $str1 . "/" contains /"" . $str2 . "/"";
}
else
{
	echo "/"" . $str1 . "/" does not contain /"" . $str2 . "/"";
}
```

正确运行的输出结果:
<!-- more -->
`"hello world" does not contain "he"`
strpos是返回字符串str2在str1的位置，没有找到则返回false；php是弱类型语言；然而实际上这次返回了0而在if语句中0也被当作false,所以我们需要对false做类型判断，正确的代码如下:

```
$str1 = 'hello world';
$str2 = 'he';
if (strpos($str1, $str2) !== false)
{
	echo "/"" . $str1 . "/" contains /"" . $str2 . "/"";
}
else
{
	echo "/"" . $str1 . "/" does not contain /"" . $str2 . "/"";
}
```
需要注意的是我们使用了!==，在php 和 JS中= !相对== 更为严格需要要求数据类型一致。

**Q2**
下面的输出结果会是怎样？

```
$x = 5;
echo $x;
echo "<br />";
echo $x+++$x++;
echo "<br />";
echo $x;
echo "<br />";
echo $x---$x--;
echo "<br />";
echo $x;
```

实际运行结果是

```
5
11
7
1
5
```

关于 $x++ 和 $x–这个问题其实非常容易遇见，我们只需记住$x++使用最近的值，然后才自增。
运算符的优先级，++ 是明显高于 +，因此先执行++ 再执行 + 。关于运算符的优先级，有的时候我们真的可以通过括号来让我们的程序更让人直观的了解，毕竟代码不光是用于执行的，有的时候或许团队的可读性也是提高效率的一种。
**Q3**
关于变量的引用；
```
$a = '1';
$b = &$a;
$b = "2$b";
```
请问 $a 和 $b的值各位多少
部分第一时间会想到 $a='1'  $b='21',仔细一看 $b=&$a,这里$b是变量$a的引用而不是直接 赋值。

**Q4**
下面是true还是false

```
var_dump(0123 == 123)；
var_dump('0123' == 123);
var_dump('0123' == 123);
```
var_dump(0123 == 123);// false,PHP会默认把0123当作8进制来处理，实际转化为10进制就是83，显然这不是相等的。
var_dump(’0123′ == 123);// true这里php会非常有趣的将’0123′转换成一个数字而且默认去掉了前面的0也就是123==123
var_dump(’0123′ === 123);// false很显然上面的问题已经说过了数字和字符串类型不一致。

**Q5**
下面的代码有什么问题吗？输出会是什么，怎样修复它

```
$referenceTable = array();
referenceTable['val1'] = array(1, 2);
referenceTable['val2'] = 3;
referenceTable['val3'] = array(4, 5);

$testArray = array();

$testArray = array_merge($testArray, $referenceTable['val1']);
var_dump($testArray);
$testArray = array_merge($testArray, $referenceTable['val2']);
var_dump($testArray);
$testArray = array_merge($testArray, $referenceTable['val3']);
var_dump($testArray);
```

实际输出如下：
```
array(2) {[0] => int(1) [1] => int(2)}
NULL
NULL
```

运行的时候你或许还能看到下面的警告

```
warning: array_merge() : Argument #2 is not an array
warning: array_merge() : Argument #1 is not an array
```
array_merge需要传入的参数都是数组，如果不是，则会返回null。 你可以这样修改

```
$testArray = array_merge($testArray, (array)$referenceTable['val1']);
var_dump($testArray);
$testArray = array_merge($testArray, (array)$referenceTable['val2']);
var_dump($testArray);
$testArray = array_merge($testArray, (array)$referenceTable['val3']);
var_dump($testArray);
```
