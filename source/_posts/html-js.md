---
title: jquery中html()、text()、val()与js中的对应属性
date: 2015-07-15 18:03
tags: JQuery
categories: 前端
---


### html() 
类似于javascript的InnerHtml属性，可以用来读取或者设置某个元素中的HTML内容  
 例如：Javascript代码  
```
    <p><strong>Hello world</strong></p>  
     alert($('p').html());   
    返回： <strong>Hello world</strong> 
```
  
 ### text()  
 类似于javascript的InnerText属性，可以用来读取或者设置某个元素的文本内容 
  例如：  Javascript代码 
 ```
    <p><strong>Hello world</strong></p>   
      alert($('p').text());   
    返回：  Hello world  
```
   
 ### val() 
 类似于javascript中的value属性，可以用来设置或者获取元素的值，无论元素是文本框，下拉列表还是单选框，如果元素时多选，则返回一个包含所有选择的值的数组。 



