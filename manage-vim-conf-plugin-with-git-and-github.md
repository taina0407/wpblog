"=========== Meta ============
"StrID : 86
"Title : 使用git和github来管理vim配置和插件
"Slug  : 
"Cats  : vim
"Tags  : vim,git
"=============================
"EditType   : post
"EditFormat : Markdown
"TextAttach : wpid97-vimpress_4ed24fb0_mkd.txt
"========== Content ==========
vim是伟大的编辑器, 但要能用得称手, 打造属于自己的配置文件和安装适合的插件是必不可少的过程。作为完美主义者，我希望整个配置的更改过程和插件的安装更新过程都有清楚的历史记录，并且配置能在不同机器间很方便的同步。

[git][]是非常流行的分布式版本管理工具. [github][]托管git仓储, 也越来越流行。 [vim scripts][]上的所有scripts都被托管到了[github][github-vim-scripts]上，很多作者也使用github来托管他们的vim plugin仓储. 

本文将介绍如何使用git和github来达成我在文章最开始提出的目标。

* 将vim的配置文件使用git管理, 并托管到github上。 这是[我的配置文件][my-vimconf]
* 直接通过github来安装和更新vim plugin

# 安装git (ubuntu)

	sudo aptitude install git-core

# 安装git-subtree

	:::bash
	mkdir $HOME/opensource
	cd $HOME/opensource
	git clone git://github.com/apenwarr/git-subtree.git
	cd git-subtree
	sudo bash install.sh

# 使用git管理.vim目录

	:::bash
	cd $HOME
	mkdir .vim
	git init
	git commit --allow-empty -m "Initial commit"
	touch vimrc
	git add vimrc
	git commit -m "Initial add vimrc"

此处省略如何将代码托管到github上, 请自行参考github的文档。
		
之后就可以在vimrc中添加你自己的配置了。为使配置生效，执行如下命令

	:::bash
	cd $HOME
	ln -s .vim/vimrc .vimrc

# 从github安装和更新vim plugin

## bootstrap: 安装vim-pathogen

安装[vim-pathogen][]之后, 可以将每个github的vim plugin工程单独放到bundle目录下，便于以后更新。

首次安装

	:::bash
	cd .vim
	mkdir bundle
	git subtree add \
		--prefix=bundle/vim-pathogen \
		--squash \
		https://github.com/tpope/vim-pathogen.git master

更新

	:::bash
	git subtree pull \
		--prefix=bundle/vim-pathogen \
		--squash \
		https://github.com/tpope/vim-pathogen.git master

配置`vim-pathogen`, 在`.vim/vimrc`中添加如下代码

	:::VimL
	runtime bundle/vim-pathogen/autoload/pathogen.vim
	call pathogen#infect()

## 从github安装其他plugin

过程类似安装vim-pathogen. 也可以使用我写的脚本[github-plugin-install.sh][]来简化安装

比如安装或更新 https://github.com/hallison/vim-markdown

	bash github-plugin-install.sh hallison/vim-markdown

该脚本会根据bundle下对应目录是否存在, 来自动确定是安装还是更新


[git]: http://git-scm.com
[github]: https://github.com/
[vim scripts]: http://vim-scripts.org/
[github-vim-scripts]: https://github.com/vim-scripts
[my-vimconf]: https://github.com/pkufranky/vimconf
[vim-pathogen]: https://github.com/tpope/vim-pathogen
[github-plugin-install.sh]: https://github.com/pkufranky/vimconf/blob/master/github-plugin-install.sh
