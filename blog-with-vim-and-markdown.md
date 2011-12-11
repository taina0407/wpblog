"=========== Meta ============
"StrID : 102
"Title : 使用vim和markdown撰写blog并发布到wordpress
"Slug  : 
"Cats  : Uncategorized
"Tags  : markdown,vim,wordpress
"=============================
"EditType   : post
"EditFormat : Markdown
"TextAttach : wpid154-vimpress_4ed265d1_mkd.txt
"========== Content ==========
$TOC$

本文介绍在vim里使用markdown撰写blog并直接发布到wordpress上。同时要支持代码的语法高亮。


# 安装必要的vim plugin

参考[前文][vim-conf-git]的方法, 安装[VimRepress][pkufranky-VimRepress]和[vim-markdown][]。前者用来与wordpress进行交互，后者用来对markdown文档进行语法高亮。

	:::bash
	bash github-plugin-install.sh vim-scripts/VimRepress
	bash github-plugin-install.sh hallison/vim-markdown

注: 为了支持code的语法高亮, 我对VimRepress做了修改，因此这里用的是我的VimRepress仓储

`VimRepress`依赖[python-markdown][], 可通过下面两种方式之一进行安装

	:::bash
	sudo aptitude install python-markdown

或

	:::bash
	sudo pip install markdown

然后在.vim/vimrc中添加类似如下配置
	
	:::vim
	let VIMPRESS=[{'username':'user', 
				   \'password':'pass', 
				   \'blog_url':'http://your-first-blog.com/' 
				   \}, 
				   \{'username':'user', 
				   \'blog_url':'http://your-second-blog.com/' 
				   \}]
	map <leader>P :BlogPreview<CR>

`password`是可选的, 如果不填, 在第一次与wordpress交互时会提示你输入密码。`VimRepress`通过`XML-RPC`与wordpress进行交互，因此，需要确保`Settings->Writing->XML-RPC`选项在wordpress中是选中的。

# 添加新的post

首先使用vim以markdown格式撰写post。写好以后，执行如下命令

	:BlogNew

将会进入一个新的window, 在文件头加入了一些meta信息, 比如post的标题和tag等。你可以编辑这些信息，但不需要保存。然后可以

## 在本地预览

使用`:BlogPreview`或 `\P`可在本地预览。

如要指定预览的浏览器，可在进入vim前设置`BROWSER`环境变量, 使用[os.pathsep][]分割多个候选浏览器。具体见[python webbrowser][].

	:::bash
	export BROWSER='w3m:lynx:links'

不知怎么回事, 如果使用links浏览器, 将不能预览中文。w3m和lynx浏览器有时不能移动光标。但w3m可以使用空格键翻页。

## 在wordpress上保存

保存为draft

	:BlogSave

或直接发布

	:BlogSave publish

# 编辑已有post

先列出所有的post

	:BlogList

在进入的新窗口中，按`<Enter>`可以编辑当前行post，然后通过`:BlogSave publish`等命令可更新post。

通过`:BlogOpen <id>`或`:BlogOpen <url>`命令, 也可直接编辑已有的post。

你可以通过`:h vimpress`查看帮助。若无帮助，请执行`:Helptags`更新文档。注意`:Helptags`由[前文][vim-conf-git]提到的[vim-pathogen][] plugin提供。

需要说明的是，编辑已有post时，仍然是markdown格式，而非html格式。其原理是这样的，当`:BlogSave`的时候，在本地使用`python-markdown`转换为html，通过XML-RPC发布到wordpress，而markdown源文件则通过附件的形式也上传到wordpress (meta中的TextAttach属性)。当编辑已有post时，是将上传的附件源文件下载下来进行编辑。

# 以本地文件而非wordpress作为数据源

上面提到修改meta的信息之后，不需要保存，因为`:BlogSave`后这些信息会保存到wordpress上，下次编辑时会自动从wordpress取下来。但如果你想以本地文件而非wordpress作为官方数据源，以便可以通过git对所有数据进行管理。你也可以将这些meta信息添加到你撰写的markdown源文件的最前面，将源文件保存并用git管理。以后如果要更新，则直接编辑此文件，并`:BlogSave publish`就可以了。(meta里有id信息)

# 目录(TOC)支持

在源码的第一行添加`$TOC$`，则在生成html时会自动将此占位符替换为目录(table of contents)，本文的目录就是这样生成的。这下明白我修改VimRepress代码时`toc(marker=$TOC$)`的意思了吧。该扩展默认是用`[TOC]`作为marker, 但这会破坏markdown源文件在vim中的语法高亮，因此我将marker换成了`$TOC$`。

# 代码语法高亮

## 修改VimRepress以支持代码高亮

python-markdown已经内置了语法高亮支持，但VimRepress没有使用，因此我对其代码做了修改以增加语法高亮支持，同时也顺便enable了其他方便的[markdown扩展][python-markdown-exts]。

代码修改示例如下

~~~~.diff
- markdown.markdown(rawtext.decode('utf-8')).encode('utf-8')
+ exts = ['meta', 'toc(marker=$TOC$)', 'def_list', 'abbr', 'footnotes', 'tables', 'codehilite', 'fenced_code']
+ html = markdown.markdown(rawtext.decode('utf-8'), exts).encode('utf-8')
~~~~

## 指定代码语言

在书写代码时，需要在第一行指定语言`:::lang` (见[CodeHilite][]), 在输出的html中, 第一行的`:::lang`会被去掉。比如

~~~.vim
	:::vim
	map <leader>p :Hammer<CR>
~~~

或使用[Fenced Code Blocks][fenced-code], 在`~~~`后添加`.lang`。

~~~~
~~~.vim
map <leader>p :Hammer<CR>
~~~
~~~~

如果只想原样输出，不希望`:::lang`被去掉，或希望输出`~~~.lang`，则只能使用`fenced code block`. 比如上面两个例子的markdown源码:

使用`:::lang`的markdown源码

~~~~
~~~.vim
:::vim
map <leader>p :Hammer<CR>
~~~
~~~~

注意上面源码中`~~~.vim`末尾的`.vim`是必须的 (必须有.lang, 但不一定是`.vim`)，否则`codehilite`会根据第一行的`:::vim`来判定语言，并在输出的html中将此行去掉。

以及使用`~~~.lang`的markdown源码

~~~~~
~~~~
~~~.vim
map <leader>p :Hammer<CR>
~~~
~~~~
~~~~~

## 对`fenced code block`扩展的修改

在目前的`python-markdown 2.1.0`版本，`fenced code block`和`codehilite`同时使用，会抛异常。

	Traceback (most recent call last):
	  File "<string>", line 1, in <module>
	  File "<string>", line 173, in __check
	  File "<string>", line 198, in __check
	  File "<string>", line 559, in blog_preview
	  File "<string>", line 39, in markdown2html
	  File "/usr/local/lib/python2.7/dist-packages/markdown/__init__.py", line 386, in markdown
		return md.convert(text)
	  File "/usr/local/lib/python2.7/dist-packages/markdown/__init__.py", line 280, in convert
		self.lines = prep.run(self.lines)
	  File "/usr/local/lib/python2.7/dist-packages/markdown/extensions/fenced_code.py", line 128, in run
		code = highliter.hilite()
	  File "/usr/local/lib/python2.7/dist-packages/markdown/extensions/codehilite.py", line 99, in hilite
		noclasses=self.noclasses)
	  File "/usr/local/lib/python2.7/dist-packages/pygments/formatters/html.py", line 347, in __init__
		self.noclasses = get_bool_opt(options, 'noclasses', False)
	  File "/usr/local/lib/python2.7/dist-packages/pygments/util.py", line 58, in get_bool_opt
		string, optname))
	pygments.util.OptionError: Invalid type [False, 'Use inline styles instead of CSS classes - Default false'] for option noclasses; use 1/0, yes/no, true/false, on/off


需要对`/usr/local/lib/python2.7/dist-packages/markdown/extensions/fenced_code.py`的104行做如下修改

~~~.diff
-                    self.codehilite_conf = ext.config
+                    self.codehilite_conf = ext.getConfigs()
~~~

## 在wordpress模板中添加高亮css样式

为了显示高亮，还要自定义css样式，并把样式添加到wordpress的模板文件中。[我的高亮样式][my-pygments-style]，取自[pygments][]的[native风格][pygments-native]，并将`syntax`替换为`codehilite`。

具体做法是，将样式文件作为附件上传到[wordpress media](/wp-admin/upload.php)中, 然后修改 `Appearance > Editor > Header`，在head中添加如下一行
	
	:::html
	<link rel="stylesheet" type="text/css" media="all" href="/wp-content/uploads/2011/12/pygments_style.css" />	

# 使用[hammer.vim][]预览markdown

上述预览和发布采用的是`python-markdown`来进行markdown到html的转换。这里再介绍另一种预览方法，使用[hammer.vim][]。 此种方法预览则没有前文提到的不能移动光标的bug。

首先安装vim hammer plugin

	:::bash
	bash github-plugin-install.sh robgleeson/hammer.vim

此插件依赖ruby, 并需要安装如下如下ruby gems

	:::bash
	sudo gem install github-markup
	sudo gem install redcarpet

然后在`.vim/vimrc`中进行配置：linux下使用`w3m`作为预览工具, 并定义预览快捷键`<leader>p`，一般是`\p`

	:::vim
	if has('unix')
		   let g:HammerBrowser = 'w3m'
	end
	map <leader>p :Hammer<CR>

这样，通过`\p`就可以预览生成的html文档了。

# References

* [Blogging with WordPress, VIM and Markdown][blog-markdown]

[pkufranky-VimRepress]: https://github.com/pkufranky/VimRepress
[vim-markdown]: https://github.com/hallison/vim-markdown
[vim-pathogen]: https://github.com/tpope/vim-pathogen
[hammer.vim]: https://github.com/robgleeson/hammer.vim
[vim-conf-git]: http://blog.pkufranky.com/2011/11/%E4%BD%BF%E7%94%A8git%E5%92%8Cgithub%E6%9D%A5%E7%AE%A1%E7%90%86vim%E9%85%8D%E7%BD%AE%E5%92%8C%E6%8F%92%E4%BB%B6/
[blog-markdown]: http://connermcd.com/2011/05/blogging-with-wordpress-vim-and-markdown-4/
[my-pygments-style]: http://blog.pkufranky.com/wp-content/uploads/2011/12/pygments_style.css
[pygments]: http://pygments.org
[pygments-native]: http://pygments.org/demo/23678/?style=native
[python-markdown]: http://www.freewisdom.org/projects/python-markdown
[python-markdown-exts]: http://www.freewisdom.org/projects/python-markdown/Available_Extensions
[CodeHilite]: http://www.freewisdom.org/projects/python-markdown/CodeHilite
[fenced-code]: http://www.freewisdom.org/projects/python-markdown/Fenced_Code_Blocks
[python webbrowser]: http://docs.python.org/library/webbrowser.html
[os.pathsep]: http://docs.python.org/library/webbrowser.html
