"=========== Meta ============
"StrID : 54
"Title : mysql编码转换分析
"Slug  : 
"Cats  : mysql
"Tags  : mysql
"=============================
"EditType   : post
"EditFormat : Markdown
"BlogAddr   : http://blog.pkufranky.com/
"========== Content ==========
$TOC$

本文详细介绍在各种配置下, 在mysql中进行插入和查询的编码转换情况。

# 初始环境
  
使用securecrt登录服务器, 其`appearance > character encoding`的值为utf8, 服务器locale为`utf8`

	:::bash
	$ locale
	LANG=en_US.UTF-8
	LANGUAGE=en_US:en
	LC_CTYPE="en_US.UTF-8"
	LC_NUMERIC="en_US.UTF-8"
	LC_TIME="en_US.UTF-8"
	LC_COLLATE="en_US.UTF-8"
	LC_MONETARY="en_US.UTF-8"
	LC_MESSAGES="en_US.UTF-8"
	LC_PAPER="en_US.UTF-8"
	LC_NAME="en_US.UTF-8"
	LC_ADDRESS="en_US.UTF-8"
	LC_TELEPHONE="en_US.UTF-8"
	LC_MEASUREMENT="en_US.UTF-8"
	LC_IDENTIFICATION="en_US.UTF-8"
	LC_ALL=

`/etc/mysql/my.cnf`中的client和mysqld的`default-character-set`均未设置，使用编译默认值`latin1`

	:::bash
	$ mysql -utest  -e 'show variables like "%char%"'  
	+--------------------------+----------------------------+
	| Variable_name            | Value                      |
	+--------------------------+----------------------------+
	| character_set_client     | latin1                     |
	| character_set_connection | latin1                     |
	| character_set_database   | latin1                     |
	| character_set_filesystem | binary                     |
	| character_set_results    | latin1                     |
	| character_set_server     | latin1                     |
	| character_set_system     | utf8                       |
	| character_sets_dir       | /usr/share/mysql/charsets/ |
	+--------------------------+----------------------------+

# 各种配置下插入和查询编码转换分析

使用的utf8编码的数据文件`data.sql`

	:::sql
	DROP DATABASE IF EXISTS test;
	CREATE DATABASE test  DEFAULT CHARACTER SET utf8;
	CREATE TABLE test.documents
	(
		  id             INTEGER PRIMARY KEY NOT NULL AUTO_INCREMENT,
		  title           VARCHAR(255) NOT NULL
	);
	INSERT INTO test.documents ( id, title) VALUES  ( 1,  '中' );

下面针对几种方案进行编码转换分析。笔者推荐在实际中使用最后一种方案，即插入和查询时都`set names utf8`。


## 插入和查询均不指定编码

	:::bash
	$ cat test0.sh
	mysql -utest < data.sql
	mysql -utest test -e 'select title, hex(title) from documents'

	$ bash test0.sh
	+-------+------------+
	| title | hex(title) |
	+-------+------------+
	| 中    | E4B8AD     |
	+-------+------------+

hex(title)为存在数据库中的实际值的hex编码，无论编码怎么变化，它的值始终不变。

* 插入: 由于`character_set_client`为`latin1`，在`data.sql`中，"中"的字节流"E4B8AD"被解释为三个latin1字符，并转化为`character_set_connection`的编码(latin1，所以不用转换）传到服务器端，然后被转化为utf8(表的编码)存入数据库。在最后一步latin1到utf8的转换中, 每个拉丁字符被转换为utf8编码(ascii码大于127的拉丁字符的utf8编码均为2个字节)，如 `E4 => C3A4`，因此，最后是hex形式为`C3A4C2B8C2AD`的字节流存入了数据库。
* 查询: utf8数据C3A4C2B8C2AD从数据库取出后，转为latin1(`character_set_results`), 为`E4B8AD`, 再转为latin1(`character_set_connection`)传回客户端, 再转为latin1(`character_set_client`)输出到屏幕。所以最后客户端输出的字节流hex形式是`E4B8AD`。由于终端是utf8编码，该字节流被显示为"中"。

## 仅插入设置character_set_client为utf8

	:::bash
	$ cat test1.sh
	( echo 'set character_set_client=utf8;' && cat data.sql ) | mysql -utest
	mysql -utest test -e 'select title, hex(title) from documents'

	$ bash test1.sh
	+-------+------------+
	| title | hex(title) |
	+-------+------------+
	| ?     | 3F         |
	+-------+------------+

* 插入: 由于`character_set_client`为utf8，在`data.sql`中，"中"的字节流"E4B8AD"被解释为一个utf8字符，并转化为`character_set_connection`的编码(latin1,转换失败,转换结果为3F）传到服务器端。之后过程同上，最后是hex形式为3F的字节流存入了数据库。
* 查询: 同上面的查询过程，只是最后一步转为utf8输出到屏幕。但3f的utf8编码仍为3F，因此最终输出的字节流为"3F"，显示为"?" 

## 仅插入时`set names utf8`

	:::bash
	$ cat test2.sh
	( echo 'set names utf8;' && cat data.sql ) | mysql -utest
	mysql -utest test -e 'select title, hex(title) from documents'

	$ bash test2.sh
	+-------+------------+
	| title | hex(title) |
	+-------+------------+
	| ?     | E4B8AD     |
	+-------+------------+

`set names utf8`将`character_set_{client,connection,results}`均设为utf8

* 插入: 整个路径全为utf8, 因此不用转换,最后hex形式为E4B8AD的字节流存入数据库
* 查询: utf8流E4B8AD从数据库数据库取出，转换为latin1(`character_set_results`)，但转换失败，转换为3F，之后不用转换，最后输出到屏幕的流为3F，显示为"?" 

## 插入和查询时均`set names utf8`

	$ cat test3.sh
	( echo 'set names utf8;' && cat data.sql ) | mysql -utest
	mysql -utest test -e 'set names utf8;select title, hex(title) from documents'

	$ bash test3.sh
	+-------+------------+
	| title | hex(title) |
	+-------+------------+
	| 中    | E4B8AD     |
	+-------+------------+

* 插入: 同上
* 查询: 整个过程不用转换，最后输出到屏幕的字节流是"E4B8AD"，显示为"中"。

# 类比

utf8编码文件`test.txt`

	$ cat test.txt
	中

该文件字节流hex形式为E4B8AD

## 类比1, 使用`iconv`转换编码

	$ iconv -f latin1 -t utf8 test.txt
	??-
	$ iconv -f latin1 -t utf8 test.txt | od -t x1
	0000000 c3 a4 c2 b8 c2 ad 0a

字节流hex形式为C3A4C2B8C2AD

## 类比2, 使用vim转换编码

	:e ++enc=latin1 test.txt
	:set fenc=utf8
	:w

三个latin1字符分别被转换为utf8字符, 字节流hex形式变为C3A4C2B8C2AD

# References

* [MySQL and UTF-8 — no more question marks!][mysql-and-utf8]

[mysql-and-utf8]: http://www.bluetwanger.de/blog/2006/11/20/mysql-and-utf-8-no-more-question-marks/
