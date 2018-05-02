---
title: 编写Oracle存储过程
date: 2015-11-19
categories: 软件开发
tags:
	- Oracle
	- 存储过程
	- SQL
---

### 整体结构

```oracle
create [or replace] procedure 过程名称(
        参数　IN|OUT|IN OUT 类型|表.字段%type [default 默认值],
        ……) AS|IS
    变量 类型|表.字段%type [default 默认值];
    ……
begin
    ……
exception
    ……
end 过程名称;
```

<!--more-->

> or replace若存在则替换  
> IN输入参数  
> OUT输出参数  
> IN OUT即可做输入参数，也可做输出参数  
> %type与表的字段同类型  
> default赋默认值（out参数不能赋默认值）  
> 过程中参数类型不用指定大小，而声明的变量类型要指定大小。如：字符类型varchar2(30)声明时需要指定30  
> AS|IS没有区别，任选一个即可  
> exception异常处理[可选]  

### 逻辑判断

```oracle
IF判断

if 条件 then
    ……
elsif 条件 then
　　……
else
    ……
end if;

```

### FOR循环：

1、自定义循环次数：  

```oracle
for i in 1..1000 loop
    ……
end loop;
```

2、利用游标循环：

```oracle
CURSOR cur IS SELECT * FROM xxx;
for cur_result in cur LOOP
    ……
END LOOP;
```

### WHILE循环：

```oracle
while 条件　loop
    ……
end loop;
```

### 异常处理

系统异常 | 异常原因
:---:|:---:
access_into_null | 未定义对象
case_not_found	case | 中若未包含相应的when，并且没有设置
collection_is_null | 集合元素未初始化
curser_already_open | 游标已经打开
dup_val_on_index | 唯一索引对应的列上有重复的值
invalid_cursor	 | 在不合法的游标上进行操作
invalid_number	 | 内嵌的 sql 语句不能将字符转换为数字
no_data_found | 使用 select into 未返回行，或应用索引表未初始化的
too_many_rows | 执行 select into 时，结果集超过一行
zero_divide	 | 除数为 0
subscript_beyond_count | 元素下标超过嵌套表或varray的最大值
subscript_outside_limit | 使用嵌套表或 varray 时，将下标指定为负数
value_error	 | 赋值时，变量长度不足以容纳实际数据
login_denied | pl/sql 应用程序连接到 oracle 数据库时，提供了不正确的用户名或密码确的用户名或密码
not_logged_on | pl/sql应用程序在没有连接 oralce 数据库的情况下访问数据问数据
program_error | pl/sql 内部问题，可能需要重装数据字典＆ pl./sql系统包统包
rowtype_mismatch | 主游标变量与 pl/sql 游标变量的返回类型不兼容
self_is_null | 使用对象类型时，在 null 对象上调用对象方法
storage_error | 运行 pl/sql 时，超出内存空间
sys_invalid_id	 | 无效的 rowid 字符串
timeout_on_resource | oracle 在等待资源时超时


### 自定义异常

```oracle
create [or replace] procedure 过程名称(
        参数　IN|OUT|IN OUT 类型|表.字段%type [default 默认值],
        ……) AS|IS
    变量 类型|表.字段%type [default 默认值];
    ……
    userException exception;
begin
    if 条件　then
        raise userException;
    end if;
    ……
exception
    when no_data_found then
        --系统异常
　　when userException then
        --自定义异常
    when others then
       --其他异常
end 过程名称;
```

### goto语句：
Oracle中没有continue关键字，在loop中可以用goto语句实现同样的效果。
goto会增加程序复杂度，可读性变差使程序不易控制和维护。
<< >>后面不能直接跟EXCEPTION这种关键字类的语句，要用NULL把标签跟关键字隔开。类似的关键字还有END LOOP之类的，等等。
 
```
declare
   i int := 1;
begin
   loop
   dbms_output.put_line('输出i=' || i);
   if i = 10 then
   goto end_loop;
   end if;
   i := i + 1;
   end loop;
   <<end_loop>>
   null;
exception
    ……
end;
```

### 注释
-- 单行注释  
/* */ 多行注释

注释必须在create [or replace] procedure之内，否则不能保存。
 

其他问题  
DBMS_OUTPUT.put_line()打印方法可用于跟踪调试  
PL/SQL DEVELOPER自带调试功能



