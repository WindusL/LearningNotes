---
title: Linux正则与文件格式化
date: 2018-03-21
categories: 
	- 软件开发
tag: 
	- Linux
	- 学习笔记
---
[TOC]
## 基础正则表达式
基础正则字符包括:`^` `$` `.` `\` `*` `[]` `[n1-n2]` `[^]` `\{n,m\}`  
### 语系对正则表达式的影响
由于不同语系的编码数据并不相同，所以就会造成数据撷取结果的差异。

<!--more-->

```
C和zh_TW.big5语系下,英文大小写的编码顺序:
LANG=C     时：0 1 2 3 4 ... A B C D ... Z a b c d ...z
LANG=zh_TW 时：0 1 2 3 4 ... a A b B c C d D ... z Z

# C语系
$ ll -d /etc/[^a-z]* # 正确显示

# zh_TW.big5语系(测试zh_CN.UTF-8语系可以正确显示)
$ ll -d /etc/[^a-z]* # 显示混乱
```

> 上面由于C语系字母是连续的所以正则表达式可以进行匹配,而zh_CN.UTF-8语系字母是非连续排列所以找不到该文件.**由于一般正则表达式，使用的是相容于 POSIX 的标准，因此就使用“ C ”这个语系,所以通常使用正则将系统语系设置成C后使用.**

```
# 搜寻特定字符串(-v表示反向)
$ grep -n (-v) 'the' regular_express.txt 
# 利用中括号[]搜寻集合字符
$ grep -n 't[ae]st' regular_express.txt
$ grep -n '[^a-z]oo' regular_express.txt
$ grep -n '[0-9]' regular_express.txt

#如上考虑到语系的影响可以使用特殊符号代替连续编码(" - ")
$ grep -n '[^[:lower:]]oo' regular_express.txt
$ grep -n '[[:digit:]]' regular_express.txt
```

> 为了要避免这样编码所造成的英文与数字的撷取问题,所以可以使用特殊符号代替


| 特殊符号   | 代表意义 |
| :-------: | :-------: |
| [:alnum:] | 代表英文大小写字符及数字，亦即 0-9, A-Z, a-z|
| [:alpha:] | 代表任何英文大小写字符，亦即 A-Z, a-z|
| [:blank:] | 代表空白键与 [Tab] 按键两者 |
| [:cntrl:] | 代表键盘上面的控制按键，亦即包括 CR, LF, Tab, Del.. 等等 |
| [:digit:] | 代表数字而已，亦即 0-9 |
| [:graph:] | 除了空白字符 （空白键与 [Tab] 按键） 外的其他所有按键 |
| [:lower:] | 代表小写字符，亦即 a-z |
| [:print:] | 代表任何可以被打印出来的字符 |
| [:punct:] | 代表标点符号 （punctuation symbol），亦即：" ' ? ! ; : # $... |
| [:upper:] | 代表大写字符，亦即 A-Z |
| [:space:] | 任何会产生空白的字符，包括空白键, [Tab], CR 等等 |
| [:xdigit:]| 代表 16 进位的数字类型，因此包括： 0-9, A-F, a-f 的数字与字符 |


## sed 工具

```
[dmtsai@study ~]$ sed [-nefr] [动作]
选项与参数：
-n  ：使用安静（silent）模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到屏幕上。
      但如果加上 -n 参数后，则只有经过 sed 特殊处理的那一行（或者动作）才会被列出来。
-e  ：直接在命令行界面上进行 sed 的动作编辑；
-f  ：直接将 sed 的动作写在一个文件内， -f filename 则可以执行 filename 内的 sed 动作；
-r  ：sed 的动作支持的是延伸型正则表达式的语法。（默认是基础正则表达式语法）
-i  ：直接修改读取的文件内容，而不是由屏幕输出。

动作说明：  [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表“选择进行动作的行数”，举例来说，如果我的动作是需要在 10 到 20 行之间进行的，则“ 10,20[动作行为] ”

function 有下面这些：
a   ：新增， a 的后面可以接字串，而这些字串会在新的一行出现（目前的下一行）～
c   ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
d   ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
i   ：插入， i 的后面可以接字串，而这些字串会在新的一行出现（目前的上一行）；
p   ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
s   ：取代，可以直接进行取代的工作！通常这个 s 的动作可以搭配正则表达式！
```

- 以行为单位的功能

	```
	# 将/etc/passwd 的内容列出并且打印行号，同时，请将第 	2~5 行删除！
	$ nl /etc/passwd | sed '2,5d'
   	1 root:x:0:0:root:/root:/bin/bash
   	6 sync:x:5:0:sync:/sbin:/bin/sync
   	7 shutdown:x:6:0:shutdown:/sbin:/sbin/		shutdown
		.....（后面省略）.....
		
	# 承上，在第二行后（亦即是加在第三行）加上“drink tea?”字样！
	$ nl /etc/passwd | sed '2a drink tea'
     1  root:x:0:0:root:/root:/bin/bash
     2  bin:x:1:1:bin:/bin:/sbin/nologin
			drink tea
     3  daemon:x:2:2:daemon:/sbin:/sbin/nologin
		.....（后面省略）.....
	```
	
- 部分数据的搜索与取代功能  
	`sed 's/要被取代的字串(或正则)/新的字串/g`  
	如果想用以行为单位取代  
	`sed 'n1,n2s/要被取代的字串(或正则)/新的字串/g`
	
- 直接修改文件内容（危险动作）  
	-i 选项可以让 sed 直接去修改后面接的文件内容而不是由屏幕输出！
	
	```
	#用 sed 将 regular_express.txt 内每一行结尾若为 . 则换成 !
	$ sed -i 's/\.$/\!/g' regular_express.txt
	
	#利用 sed 直接在 regular_express.txt 最后一行加	入“# This is a test”
	$ sed -i '$a # This is a test' 	regular_express.txt
	# 由于 $ 代表的是最后一行，而 a 的动作是新增，因此该	文件最后新增！
	```

## 延伸正则表达式
grep 默认仅支持基础正则表达式，如果要使用延伸型正则表达式，你可以使用 grep -E ， 不过更建议直接使用 egrep

```
#去除空白行与首先为#的行列(需要使用管线命令来搜寻两次)
grep -v '^$' | grep -v '^#' regular_express.txt

#使用延伸正则表达式(延伸型正则表达式可以通过群组功能“ | ”来进行一次搜寻！)
egrep -v '^$|^#' regular_express.txt
```

延伸正则字符包括:`+` `?` `|` `()` `()+`

## 文件格式化处理
### 格式化打印

```
printf '打印格式' 实际内容
选项与参数：
关于格式方面的几个特殊样式：
       \a    警告声音输出
       \b    倒退键（backspace）
       \f    清除屏幕 （form feed）
       \n    输出新的一行
       \r    亦即 Enter 按键
       \t    水平的 [tab] 按键
       \v    垂直的 [tab] 按键
       \xNN  NN 为两位数的数字，可以转换数字成为字符。
关于 C 程序语言内，常见的变量格式
       %ns   那个 n 是数字， s 代表 string ，亦即多少个字符；
       %ni   那个 n 是数字， i 代表 integer ，亦即多少整数码数；
       %N.nf 那个 n 与 N 都是数字， f 代表 floating （浮点），如果有小数码数，
 假设我共要十个位数，但小数点有两位，即为 %10.2f ！
```

### awk工具



