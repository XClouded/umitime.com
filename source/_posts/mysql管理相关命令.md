title: mysql管理相关命令
tags:
  - database
  - mysql
date: 2012-02-29 09:56:47
---

mysql&gt; show table status like &#8216;mytable&#8217; \G;

*************************** 1\. row ***************************

Name: mytable  表名

Engine: InnoDB  存储引擎伟InnoDB

Version: 10   mysql版本

Row_format: Compact   行格式。有Dynamic，fixed，Compact等格式。Dynamic是动态行，表字段里面宝航varchar，BloB等不定长字段。fixed是定长行。Compact是行压缩。

Rows: 0  表中的行数

Avg_row_length: 0  平均每行的字节数

Data_length: 16384   整个表的数据量（字节）

Max_data_length: 0  表最大的容量。0表示无限

Index_length: 0    索引数据占用磁盘空间的大小

Data_free: 10485760  表示已分配但还未被使用的空间大小。

Auto_increment: NULL  下一个AUto_increment的值

Create_time: 2011-08-06 22:39:46  创建时间

Update_time: NULL  更新时间

Check_time: NULL  使用check table等命令时的检查时间

Collation: utf8_general_ci  默认字符集和字符列排列顺序

Checksum: NULL  如果启动，则表示整个表的校验和

Create_options: max_rows=4294967295 avg_row_length=32  表创建时的选项

Comment:

1 row in set (0.00 sec)