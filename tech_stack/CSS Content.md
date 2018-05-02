---
title: CSS Content
date: 2015-04-11
categories: 软件开发
tags:
	- CSS
	- HTML
	- WEB
---

### 定义与用法  
content 属性与 :before 及 :after 伪元素配合使用，来插入生成内容。该属性用于定义元素之前或之后放置的生成内容。默认地，这往往是行内内容，不过该内容创建的框类型可以用属性 display 控制。

值	| 描述
:---:|:---:
none	| 
normal |	 
content specifications	 |
inherit	| 规定应该从父元素继承 content 属性的值。

<!--more-->

```html
<style>
div:before{
 content:"书记说：";
 color:red;
}
</style>
<div>同志们好！</div>
```

### 浏览器支持
所有浏览器都支持 content 属性。


### 特殊字符
content属性同样支持ASCII 特殊字符，具体字符请参考(X)HTML的特殊字符，如果想要查询某个符号的数值可以使用the Entity Conversion Calculator进行转换或者反向转换。

```html
<style>
div:before{
 content:"\2660";
 color:red;
}
</style>
<div>A（红桃A）</div>
```


### 属性的使用
content属性还可以通过attr(属性名)方式与标签属性相结合使用。

```html
<style>
div:before{
 content:attr(title);
 color:red;
}
</style>
<div title="副">总经理说：全体涨工资！</div>
```


### 功能进阶
与CSS3相配合实现类似title属性的功能，但是比title体验效果好。

```html
<style>
a {
  color: #900;
  text-decoration: none;
}

a:hover {
  color: red;
  position: relative;
}

a[tip]:hover:after {
  content: attr(tip);
  padding: 4px 8px;
  color: #333;
  position: absolute;
  left: 0; 
  top: 100%;
  white-space: nowrap; 
  z-index: 20px;
  -moz-border-radius: 5px; 
  -webkit-border-radius: 5px;  
  border-radius: 5px;  
  -moz-box-shadow: 0px 0px 4px #222;  
  -webkit-box-shadow: 0px 0px 4px #222;  
  box-shadow: 0px 0px 4px #222;  
  background-image: -moz-linear-gradient(top, #eeeeee, #cccccc);  
  background-image: -webkit-gradient(linear,left top,left bottom,color-stop(0, #eeeeee),color-stop(1, #cccccc));
  background-image: -webkit-linear-gradient(top, #eeeeee, #cccccc);  
  background-image: -moz-linear-gradient(top, #eeeeee, #cccccc);  
  background-image: -ms-linear-gradient(top, #eeeeee, #cccccc);  
  background-image: -o-linear-gradient(top, #eeeeee, #cccccc);  
}
</style>
<a href="#" tip="鼠标已悬浮！">请把鼠标悬浮在此！</a>
```

