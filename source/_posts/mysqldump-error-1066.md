title: mysqldump error 1066
tags:
  - database
  - error 1066
  - mysqldump
date: 2011-11-07 11:09:47
---

mysqldump -uroot -p database &gt; database.sql报错：

mysqldump: Got error: 1066: Not unique table/alias: &#8216;grc_remarks&#8217; when using LOCK TABLES

解决方法是添加参数&#8211;single-transaction 代替 &#8211;lock-tables：

mysqldump &#8211;single-transaction -uroot -p database &gt; database.sql