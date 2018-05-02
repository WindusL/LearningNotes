---
title: 网页head内容跑到了body中
date: 2014-09-29
categories: 软件开发
tags:
	- BOM
	- PHP
	- HTML
---

最近发现博客的favicon图标在chrome浏览器中不显示，但在IE中却可以，于是我打开调试工具查看，结果发现HTML head标签的内容全部跑到body中去了，由于favicon必须在head标签中所以现在不能显示了。于是排查可能出现的问题：

> 1.存在标签未闭合等问题  
> 2.存在移动head内容的js代码等问题  
> 3.存在编码或乱码等问题  

<!--more-->

经过检查后排除了1.2项的问题于是把注意力集中在第三个问题上，经过一阵咕弄终于发现了由于文件保存的时候编码存在BOM标记。

BOM是Windows下记事本软件保存一个UTF-8编码的文件时开头正文的一部分来表明编码方式的隐藏字符，对于一般文件貌似没有什么太大麻烦，但对于PHP来说却不是个福星，它会被PHP完全读取出来，使整个网页向下填充无法紧贴浏览器顶部，并且可能出现标签混乱。

既然找到了问题就着手解决问题吧。

- 一种方法是手动将文本在编辑器中打开，将编码转换为UTF-8无BOM编码方式，当然如果你的文件太多这个方法就不太适用了。

- 第二种方法如下  

```php
<?php 
$basedir = str_replace('/clearBOM.php','',str_replace('\\','/',dirname(__FILE__)));
$auto = 1;
checkdir($basedir);
function checkdir($basedir){
	if ($dh = opendir($basedir)) {
		while (($file = readdir($dh)) !== false) {
			if ($file != '.' && $file != '..'){
				if (!is_dir($basedir.'/'.$file)) {
					$filename = $basedir.'/'.$file;
					echo 'filename:'.$basedir.'/'.$file.checkBOM($filename).'<br>';
				} else {
					$dirname = $basedir.'/'.$file;
					checkdir($dirname);
				}
			}
		}
		closedir($dh);
	}
}

function checkBOM ($filename) {
	global $auto;
	$contents = file_get_contents($filename);
	$charset[1] = substr($contents, 0, 1);
	$charset[2] = substr($contents, 1, 1);
	$charset[3] = substr($contents, 2, 1);
	if (ord($charset[1]) == 239 && ord($charset[2]) == 187 && ord($charset[3]) == 191) {
		if ($auto == 1) {
			$rest = substr($contents, 3);
			rewrite ($filename, $rest);
			return '<font color=red>BOM found,automatically removed.</font>';
		} else {
			return '<font color=red>BOM found.</font>';
		}
	} else {
		return 'BOM Not Found.';
	}
}

function rewrite ($filename, $data) {
	$filenum = fopen($filename, 'w');
	flock($filenum, LOCK_EX);
	fwrite($filenum, $data);
	fclose($filenum);
}
?>
```

将上面代码保存为PHP文件（注意编码格式哦），然后上传后在浏览器中执行一下就OK了，看一下效果，搞定收工……^^!



