"=========== Meta ============
"StrID : 106
"Title : 使用url rewrite修改下载文件名
"Slug  : 
"Cats  : Uncategorized
"Tags  : 
"=============================
"EditType   : post
"EditFormat : Markdown
"TextAttach : wpid122-wpid-wpid-vimpress_4ee35da3_mkd.txt
"========== Content ==========

目前服务器以如下形式存储apk文件

	com.foo/app.apk
	com.bar/app.apk

下载url类似`http://d.ee168.cn/com.foo/app.apk`，这导致用户下载下来的文件名全是app.apk，很不友好。在不改动文件名字的情况下，想使用包名来作为下载文件名，且不想写代码，仍要直接使用apache来serve文件。目前采用的方案如下，将下载地址改为类似`http://d.ee168.cn/cachedapks/com.foo/com.foo.apk`的形式，在apache VirtualHost中使用url rewrite来重定向到真实地址。

	:::ApacheConf
	RewriteEngine On
	RewriteOptions Inherit
	RewriteCond $0 ^cachedapks/
	RewriteRule cachedapks/(.*)/.*.apk$ /apks/$1/app.apk [L]

注意还要enable mod_rewrite

	:::bash
	cd /etc/apache2/mods-enabled
	sudo ln -s ../mods-available/rewrite.load
	sudo service apache2 reload

这里是[mod_rewrite的文档](http://httpd.apache.org/docs/2.0/mod/mod_rewrite.html)
