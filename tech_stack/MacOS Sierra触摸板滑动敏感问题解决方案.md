---
title: MacOS Sierra触摸板滑动敏感问题解决方案
date: 2016-10-08
categories: 软件开发
tags:
	- MacOS
---

升级完macOS Sierra系统后，JAVA开发工具滚动条过于敏感问题，原因是由于JDK和Sierra不兼容，由于 Sierra 的触发事件参数比之前系统都复杂一些，而JAVA统一将这些参数处理为默认值，导致滚动速度快了很多，事实上可以发现升级系统后所有Java应用都存在这个问题，还包括Eclipse。。

<!--more-->

下载openJDK112版本：

http://bintray.com/jetbrains/intellij-jdk/download_file?file_path=jbsdk8u112b403_osx_x64.tar.gz
解压后，移动到/Library/Java/JavaVirtualMachines
打开idea,双击shift，弹出对话框，输入Switch IDE JDK

选择选项后，将openJDK跟换为刚才下载的版本

重启！


原文：https://youtrack.jetbrains.com/issue/IDEA-158500


