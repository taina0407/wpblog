"=========== Meta ============
"StrID : 63
"Title : 使用secureCRT和screen实现多任务窗口的快速切换
"Slug  : multiple-task-switch-with-securecrt-and-screen
"Cats  : kuxun, screen
"Tags  : 
"=============================
"EditType   : post
"EditFormat : Markdown
"BlogAddr   : http://blog.pkufranky.com/
"========== Content ==========
Screen原来的一个用处是取代nohup，当终端关闭的时候，保证程序仍然能继续运行。但如今的linux kernel已经能够支持这个功能了。所以这里谈谈screen的另外的用途。

我们可能需要同时进行多个任务，或者一个任务涉及多个模块，导致我们不得不开很多个securecrt的tab，或者频繁的切换目录。这样的工作其实会消耗大量的时间，特别是当tab比较多的时，找到想切换的tab还得费不少时间。而且tab的状态不方便持久保存，当重启机器之后，恢复之前的工作环境也要费不少时间。

而采用securecrt tab，结合screen可以更方便的帮我们完成任务窗口的切换。更进一步，可以把vim的分窗口功能也用上。（其实screen还支持将屏幕分成上下2部分，每个部分显示不同的shell session，不过这个就暂不介绍了）

为了大家有直观的印象，下面是我工作的一个截图。

![securecrt screen](http://pic.yupoo.com/pkufranky/BSRqNs4R/119hkK.jpg)

概括来讲，就是

- 每个服务器一个securecrt tab (或多个tab)
- 每个tab下运行screen (当然，若那个tab不需要多任务，就不需要screen)
- 每个screen下多个screen window，如图中的php, ds, ts等等
- 在一个screen window下面，起vim，vim也可以分屏成多个vim window

不要觉得我夸张，这就是我一天的工作情况。大部分的tab和window，我每天都会用到。

下面就讲讲screen的使用

首先下载[screenrc](http://github.com/pkufranky/shellconf/blob/master/screenrc)存为`$HOME/.screenrc`，有了该配置文件，才会列出每个screen窗口的名字。
screen的一些常用命令如下:

- 启动一个screen: screen
- 给当前screen窗口起一个名字(如上面的php, ds): ctrl-a A
- 创建一个新的screen窗口: ctrl-a c
- 在screen窗口间切换: ctrl-a 0, ctrl-a 1, ..., ctrl-a 9切换到对应序号的窗口,
  ctrl-a a 切换到之前访问的窗口. 可以创建超过9个窗口，不过就不能用上面的方式切换到序号大于9的窗口了，但可以用ctrl-a n和ctrl-a p切换到下一个或上一个窗口
- 将screen切换到后台: ctrl-a z (类似于将普通程序切换到后台的ctrl-z，但在使用了screen的情况下，ctrl-z只会将当前screen窗口正在运行的程序切换到后台，而不会将screen本身切换到后台)，使用fg又可重新回到screen屏幕
- 锁定screen: ctrl-a x，需要输密码才能解锁

假设你已经启动了screen，然后你的机器关闭了或你关闭了securecrt。没有关系，你的所有工作状态都完全保存在服务器。即使是你正在修改的没有保存的内容也不会丢失！
当你重新登陆回服务器后，使用下面的命令能看到有几个screen正运行在服务器上

	$ screen -ls
	There are screens on:
			16668.pts-2.kooxoo146   (Detached)
			9184.pts-3.kooxoo146    (Detached)
			30034.pts-19.kooxoo146  (Detached)

如果只有一个screen运行，使用`screen -r`就可切换回之前的工作状态
如果有多个screen运行，则使用如`screen -r 1666`就可以切换到上面列出的第一个`screen 16668.pts-2.kooxoo146`  (注意我没有使用完整的名字，使用能唯一标识的前几位就可以了)

至于vim的分屏，这里就先不介绍了。

另外找了篇介绍screen的文章

http://www.cnblogs.com/ppyyr/archive/2008/05/28/1208779.html
