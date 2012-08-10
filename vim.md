中文vim文档

http://vimcdoc.sourceforge.net/
http://vimcdoc.sourceforge.net/doc/help.html

vim ppt
http://www.slideshare.net/c9s/vim-hacks


modes

	Insert
		i - insert
		I
		a - append
		A
	Normal
		ESC
	Visual
		v, V, CTRL-V


editing

    :h change.txt
    :h motion.txt
    :h operator
    :h motion

    v|c|d|y i|a {[("'
    operator + region
    region: motion, text object, visual selection


    operator
        :h operator
        operator前可加数字表明执行几次
        operator分行模式和字符模式, 可通过在operator后加v, V或CTRL-V切换

        c - change
        d - delete
        y - yank into register

        > - 向右缩进
        < - 向左缩进

        >> - 向右缩进一个shiftwidth
        << - 向左缩进一个shiftwidth
        dd - 删除整行
        cc - 删除当前行的文字并进入插入模式
        yy - 拷贝整行
        D - 删除直到行尾
        C - 删除直到行尾并进入插入模式
        x = dl

    motion
        :h motion

        单独使用或operator之后
        motion前可跟数字表示重复执行指定次数

        h,j,k,l
        gk,gj
        H, M, L
        f{char}
        F{char}
        t{char}
        T{char}
        w - 下一个word
        ^,0 - 行首
        $ - 行尾
        [{ - 到上一级未匹配的{
        ]}
        % - 匹配括号 ({[
        gg - 文件首
        G - 文件尾
        '' - 跳回上一个位置

    text object selection

        visiual mode或operator之后

        an object
        inner object

        a{
        a[
        aw
        i{
        i[

     Example
        
        dj - 删除本行和下一行
        dvj - 删除当前字符知道按j后的那个字符
        dv2j = vjjd = v2jd
        dVj - 同dj
        dCTRL-Vj - 删除本行和下一行的当前列的字符

列编辑

    CTRL-VjjjIblabla<ESC> - 连续4列的当前位置插入blabla
    CTRL-Vjjjd - 删除连续4行的当前位置字符

拷贝剪切粘贴

    任何删除命令会把删除的内容放到默认寄存器, 按p可以粘贴

    将内容放入寄存器a并粘贴
    "add
    "ap

    使用系统剪贴板

        剪切
        "+dd
        "+p

        拷贝粘贴
        "+yy
        "+p

fold

	fold methods

		marker {{{, }}}
		indent
		syntax (eclipse method fold)

	:set fdl=0 - 折叠所有 (0及以下level)
	:set fdm=marker

    zm - fdl - 1
	zM - set fdl=0
    zc, zC - 折叠光标处上一层次/所有层次
	zo, zO - 展开当前/所有折叠
    zR - 展开所有折叠

tabs

	:tabe path - 打开新tab编辑文件
	gt - 下一个tab
    2gt - 下2个tab
	gT - 上一个tab

window

	:vs - 竖直分屏
	:sp - 水平分屏
	^w j/k/h/l - 在window间跳转
	^w J/K/H/L 将当前窗口移到最下/上/左/右
	^w x - 交换窗口
	^w c - 关闭窗口
	^w O - 当前window变为唯一窗口
	^w T - 当前窗口在新的tab打开

编辑文件

	:e path - 打开文件
	:e ++enc=gbk path - 以gbk编码打开文件
	:set ff
    :set ft

编码
    fileencodings
    inner encoding - unicode
    fileencoding
    termininal encoding

mode line

# vim: fdm=marker:sw=2:ts=2:et:fdl=0

其他

	:%s/foo/bar/g - 全文替换foo为bar
    :%s/CTRL-V<Enter>//g
    :set list

    u - 回退
	p - put 粘贴到后面
	P - 粘贴到前面
	O - 换行并进入插入模式. open a new line

    r{char} - 替换当前字符为{char}
    s - 删除当前字符并进入插入模式
	^N - 补全
	=G - 格式化指导文件尾
    J - 连接相邻两行
    {Visula}J - 连接选中的多行
    CTRL-] - 跳转
    CTRO-O - 回调
    /, ? - 查找
    *, # - 查找当前词
    space - 向下翻页
    ctrl-B - 向上翻页

