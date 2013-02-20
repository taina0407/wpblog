
http://qt-project.org/doc/qt-4.8/qnetworkaccessmanager.html#details


QNetworkReplyProxy
	http://stackoverflow.com/questions/4649378/how-to-view-html-data-associated-with-a-qwebpage
	http://gitorious.org/qtwebkit/performance/blobs/master/host-tools/mirror/main.cpp

http://www.tylerlesmann.com/2009/oct/01/web-scraping-pyqt4/

http://stackoverflow.com/questions/6011866/qt-qwebview-and-qbytearray-cause-encoding-conflict
http://stackoverflow.com/questions/14987007/what-is-the-expected-encoding-for-qwebviewsethtml
> There is no expected encoding because the text should already have been decoded to 16-bit unicode when you created the QString. It's up to you to do that correctly, but if you used the QString(const QByteArray&) constructor then Qt will by default treat the contents as ASCII.
> If you want to treat the content as UTF-8 then you can use QString::fromUtf8. If you need to do something more sophisticated you can use QTextCodec to read many different encodings.

> I think the source of confusion was that the encoding is taken from meta tag when loading a file but assumed to be 16-bit unicode when setting from QString, so essentially charset=... is ignored.

http://pyqt.sourceforge.net/Docs/PyQt4/qtwebkit.html
http://pyqt.sourceforge.net/Docs/PyQt4/qwebpage.html

http://pyqt.sourceforge.net/Docs/PyQt4/qstring.html#details
> The QString class provides a Unicode character string.

http://stackoverflow.com/questions/2426053/how-to-get-response-in-qtwebkit

http://pyqt.sourceforge.net/Docs/PyQt4/gotchas.html#python-strings-qt-strings-and-unicode

http://blog.sciencenet.cn/home.php?modśpace&uid=600900&do=blog&id=498237

> Whenever PyQt expects a QString as a function argument, a Python string object or a Python Unicode object can be provided instead, and PyQt will do the necessary conversion automatically.
You may also manually convert Python string and Unicode objects to QString instances by using the QString constructor as demonstrated in the following code fragment:

	qs1 = QtCore.QString("Converted Python string object")
	qs2 = QtCore.QString(u"Converted Python Unicode object")

但可惜这只适用于英文即ascii编码，对于中文则行不通！

直接的QString：
	>>> QtCore.QString('中文')
	PyQt4.QtCore.QString(u'"xd6"xd0"xce"xc4')
	>>> print QtCore.QString('中文')
	Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
	UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-3: ordin
	al not in range(128)
	>>>
	>>> QtCore.QString(u'中文')
	PyQt4.QtCore.QString(u'"u4e2d"u6587')
	>>> print QtCore.QString(u'中文')
	Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
	UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordin
	al not in range(128)
	>>>

因为它们都是默认按ascii编码转换！


Python中需要用Python string object和Python Unicode object的地方可就不一定可以直接用QString了!!!
QString向Python string object转换可以理解，因为编码不同。
QString向Python Unicode object的转换？需要转换吗？不都是utf16编码吗？
QString是tuf16编码，但是它的实现并非Python Unicode object那样直接的utf16码，而实际是一个QChar串，每个QChar才对应unicode符，所以地位相当但并不相同。
许多英文书籍写到：可以使用str()函数直接将QString转换为Python string object，可以使用unicode()直接将QString转换为Python Unicode object。如《PyQt手册》：

In order to convert a QString to a Python string object use the Python str() builtin. Applying str() to a null QString and an empty QString both result in an empty Python string object.

In order to convert a QString to a Python Unicode object use the Python unicode() builtin. Applying unicode() to a null QString and an empty QString both result in an empty Python Unicode object.

但同样只适用于英文，具体见下面分别分析。
1）QString向Python Unicode object的转换。

	>>> from PyQt4 import QtGui, QtCore
	>>> unicode(QtCore.QString('def'))
	u'def'
	>>> print unicode(QtCore.QString('def'))
	def


http://docs.huihoo.com/pyqt/pyqt4.html#python-strings-qt-strings-and-unicode

> Unicode support was added to Qt in v2.0 and to Python in v1.6. In Qt, Unicode support is implemented using the QString class. It is important to understand that QString instances, Python string objects and Python Unicode objects are all different but conversions between them are automatic in almost all cases and easy to achieve manually when needed.
> 
> Whenever PyQt expects a QString as a function argument, a Python string object or a Python Unicode object can be provided instead, and PyQt will do the necessary conversion automatically.
> 
> You may also manually convert Python string and Unicode objects to QString instances by using the QString constructor as demonstrated in the following code fragment:
> 
> qs1 = QtCore.QString("Converted Python string object")
> qs2 = QtCore.QString(u"中国Converted Python Unicode object")
> In order to convert a QString to a Python string object use the Python str() builtin. Applying str() to a null QString and an empty QString both result in an empty Python string object.
> 
> In order to convert a QString to a Python Unicode object use the Python unicode() builtin. Applying unicode() to a null QString and an empty QString both result in an empty Python Unicode object.
> 
> QString also implements Python's buffer protocol which means that a QString can be used in many places where a Python string or Unicode object is expected without being explicitly converted.

	>>> unicode(QtCore.QString(u'中国'))
	u'\u4e2d\u56fd'


https://qt-project.org/doc/qt-4.8/qwebframe.html#setHtml
> When using this method WebKit assumes that external resources such as JavaScript programs or style sheets are encoded in UTF-8 unless otherwise specified. For example, the encoding of an external script can be specified through the charset attribute of the HTML script tag. It is also possible for the encoding to be specified by web server.

http://encoding.spec.whatwg.org/#encodings

> Authors must use the utf-8 encoding and must use the "utf-8" label to identify it.



# 页面申明编码为gb2312, 若有gbk字符, 会出乱码

http://mail.kde.org/pipermail/kde-china/2010-October/004227.html

> 我有空看看把 webkit 的编码探测弄到 qtwebkit
> （印象中 qtwebkit 不用 icu，所以那个那啥那啥 nihui ）
> 
> 不过关于 gb2312 和 gb18030 别名的破事实在是 XX 了很多年啊，诺基亚不知道怎么想的


http://osdir.com/ml/kde-china/2010-10/msg00005.html

2010/10/14 nihui <shuizhuyuanluo在126.com>

> 原本我以为在 rekonq 那边是可以改的，没想到只能在 qt 里面改
> qwebsettings 的 defaulttextencoding 只是在 html/xml 没有 charset 信息的时候才会起作用，所以
> rekonq 的改编码菜单实际上也只能应付没有 charset 信息的页面，acfun.cn 的那个页面指定
> gb2312，所以即使自己改浏览器编码设置也是没用的。
> webkit 自己有编码探测的功能，可以把 gb2312 认作 gbk，gbk 基本不会有乱码了，但是 qwebsettings
> 没有把这个功能暴露出来，默认禁用，所以浏览器那边还是没办法。
>
> qt-4.7.0-webkit-htmlxml-gb-gb18030.patch 这个补丁可以解决类似 acfun.cn 这类页面的问题。
> 有谁能帮我提交到 qt 和 webkit 的源码里么，自己 git clone 要求合并实在是太太太太慢了。。。。
>
> freeflying 能帮忙给 kubuntu 打上第一个补丁么.......
>
> 另外还有个更加 dirty 的补丁，qt-4.7.0-qwebsettings-gb18030-default.patch，用在当没有 charset
> 信息的时候可以当做 gb18030，而不是 iso-8859-1。
> 还有就是强烈要求 qwebsettings 加上自动探测编码的 api !!!
>
> 参考截图
> http://theqii.info/blog/?p=11990
> http://theqii.info/blog/?p=11995
>
> nihui
>

https://github.com/mkedwards/crosstool-ng/blob/master/patches/qt/4.7.3/kubuntu_90_webkit_htmlxml_gb_gb18030_detect.diff

[change gb2312/gbk to gb18030 in html/xml files](https://bugreports.qt-project.org/browse/QTBUG-15407?page=com.atlassian.jira.plugin.system.issuetabpanels:changehistory-tabpanel)


http://problemssol.blogspot.jp/2010/12/compile-and-install-pyqt4-for-python27.html

https://pypi.python.org/pypi/PyQt/4.10.2

[Bug 98798 - Remap GB2312 and GBK encoding to GB18030](https://bugs.kde.org/show_bug.cgi?id=98798)


本质应该是这个bug, 还没fix

[QtWebKit does not apply correct encoding on some pages with CJK characters](https://bugs.webkit.org/show_bug.cgi?id=73519)

qt已经解决了这个问题
http://code.woboq.org/qt5/qtwebkit/Source/WebCore/platform/text/TextCodecICU.cpp.html
http://qt.gitorious.org/qt/qtwebkit/blobs/stable/Source/WebCore/platform/text/TextCodecICU.cpp


	88	        // 1. Treat GB2312 encoding as GBK (its more modern superset), to match other browsers.
	89	        // 2. On the Web, GB2312 is encoded as EUC-CN or HZ, while ICU provides a native encoding
	90	        //    for encoding GB_2312-80 and several others. So, we need to override this behavior, too.
	91	        if (strcmp(standardName, "GB2312") == 0 || strcmp(standardName, "GB_2312-80") == 0)
	92	            standardName = "GBK";

http://src.chromium.org/chrome/branches/official/build_149.27/src/webkit/pending/TextCodecICU.cpp


        // Here, we used to alias GB2312 and GB2312-80 to GBK, but our copy
        // of ICU treats GB2312/GB2312-80 as synonyms of GBK so that we
        // don't need that any more.
        registrar(standardName, standardName);

WebKit从Qt 4.4开始被作为一个Module被集成到Qt中。

Qt 4.7 使Qt WebKit集成的稳定性和性能均得到提升的更新。

http://qt-project.org/wiki/Building_Qt_5_from_Git


aptitude show libqtwebkit4
Version: 2.2.1-4+b1

http://samos-it.com/install-pyqt-in-a-virtualenv-with-pip/


http://pyqt.sourceforge.net/Docs/PyQt4/gotchas.html#access-to-protected-member-functions

> When an instance of a C++ class is not created from Python it is not possible to access the protected member functions, or emit any signals, of that instance. Attempts to do so will raise a Python exception. Also, any Python methods corresponding to the instance’s virtual member functions will never be called.
