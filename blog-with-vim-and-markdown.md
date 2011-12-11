"=========== Meta ============
"StrID : 102
"Title : 使用vim和markdown撰写blog并发布到wordpress
"Slug  : 
"Cats  : Uncategorized
"Tags  : markdown,vim,wordpress
"=============================
"EditType   : post
"EditFormat : Markdown
"TextAttach : wpid134-vimpress_4ed265d1_mkd.txt
"========== Content ==========
本文介绍在vim里使用markdown撰写blog并直接发布到wordpress上。


# 安装必要的vim plugin

参考[前文][vim-conf-git]的方法, 安装[VimRepress][]和[vim-markdown][]。前者用来与wordpress进行交互，后者用来对markdown文档进行语法高亮。

	:::bash
	bash github-plugin-install.sh vim-scripts/VimRepress
	bash github-plugin-install.sh hallison/vim-markdown

`VimRepress`依赖`python-markdown`, 因此需要通过下面两种方式中的一种进行安装

	:::bash
	sudo aptitude install python-markdown

或

	:::bash
	sudo pip install markdown2

然后在.vim/vimrc中添加类似如下配置
	
	:::VimL
	let VIMPRESS=[{'username':'user', 
				   \'password':'pass', 
				   \'blog_url':'http://your-first-blog.com/' 
				   \}, 
				   \{'username':'user', 
				   \'blog_url':'http://your-second-blog.com/' 
				   \}]

`password`是可选的, 如果不填, 在第一次与wordpress交互时会提示你输入密码。`VimRepress`通过`XML-RPC`与wordpress进行交互，因此，需要确保`Settings->Writing->XML-RPC`选项在wordpress中是选中的。

# 添加新的post

首先使用vim以markdown格式撰写post。写好以后，执行如下命令

	:BlogNew

将会进入一个新的window, 在文件头加入了一些meta信息, 比如post的标题和tag等。你可以编辑这些信息，但不需要保存。然后可以

在本地预览

	:BlogPreview

或在wordpress上保存为draft

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

# 使用[hammer.vim][]预览markdown

`:BlogPreview local`不知为什么，不能预览中文。因此我采用的是[hammer.vim][]来进行预览。首先安装vim plugin

	:::bash
	bash github-plugin-install.sh robgleeson/hammer.vim

此插件依赖ruby, 并需要安装如下如下ruby gems

	:::bash
	sudo gem install github-markup
	sudo gem install redcarpet

然后在`.vim/vimrc`中进行配置：linux下使用`w3m`作为预览工具, 并定义预览快捷键`<leader>p`，一般是`\p`

	:::VimL
	if has('unix')
		   let g:HammerBrowser = 'w3m'
	end
	map <leader>p :Hammer<CR>

这样，通过`\p`就可以预览生成的html文档了。

# References

* [Blogging with WordPress, VIM and Markdown][blog-markdown]

[VimRepress]: https://github.com/vim-scripts/VimRepress
[vim-markdown]: https://github.com/hallison/vim-markdown
[vim-pathogen]: https://github.com/tpope/vim-pathogen
[hammer.vim]: https://github.com/robgleeson/hammer.vim
[vim-conf-git]: http://blog.pkufranky.com/2011/11/%E4%BD%BF%E7%94%A8git%E5%92%8Cgithub%E6%9D%A5%E7%AE%A1%E7%90%86vim%E9%85%8D%E7%BD%AE%E5%92%8C%E6%8F%92%E4%BB%B6/
[blog-markdown]: http://connermcd.com/2011/05/blogging-with-wordpress-vim-and-markdown-4/
