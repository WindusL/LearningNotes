---
title: Linux上分不清的查询指令
date: 2014-05-05
categories: 软件开发
tags:
	- Linux
---

**whatis [指令或数据]:**

列出相关指令的说明文件（相当于man -f）

**apropos [指令或数据]:**

列出系统的说明文件中，相关指令或关键词（相当于man -k）

> whatis/apropos两个指令想要使用必须用makewhatis(root权限)建立whatis数据库。

<!--more-->

**whois:**

查找并显示指定帐号(或域名)的用户相关信息，因为它是到Network Solutions的WHOIS数据库去查找，所以该帐号名称必须在上面注册方能寻获，不区分大小写。

**whereis [-bmsu] 档案或目录名:**

从指定目录中搜寻特定档案。

```
-b : 查找binary格式档案
-m : 查找在说明文件manual路径下的文件
-s : 查找source来源档案
-u : 查找不在上述档案中的其它特殊档案
```

**locate [-ir] 搜寻字符串:**

和whereis类似locate是从数据库中搜寻档案，由于数据库默认每天更新一次，所以会有数据不实时的限制问题。（如：最新文档搜寻不到、已删除档案仍然存在等问题）

updatedb指令可以读取/etc/updatedb.conf,然后在硬盘中搜寻文件动作，最后更新/var/lib/mlocate数据库。
locate：依据 /var/lib/mlocate 内癿数据库记载，找出用户输入癿关键词文件名。

**which:**

从PATH路径中搜寻执行档（默认显示第一个，加-a显示全部）

**type [-typa] name:**

查找指令类型（如：内建指令builtin）,如果后面名称不能以执行档的状态找到，那该名称不会被找到。也可以作为which用来找指令。

**file:**

查看档案基本数据的类型（如：ASCII、data 档案、binary等），并且其中没有使用到动态函式库。

**find [选项] :**

功能强大的搜寻指令，由于是硬盘操作，速度稍慢。



