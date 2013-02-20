
	^-b [  进入copy模式
	space  开始选择
	Enter  copy
	^-b ]  paste
	^-b #  list buffers
	^-b =  select buffer to paste
	^-b:showb show current buffer
	^-b $ rename session
	^-b " split window
	^-b % split window
	^-b l last window

每个用户默认仅能启动一个tmux server, server一个进程, 每个client一个进程。client和server的通信通过socket进行
所有session都在server进行管理

server
	sessions
		windows
			panes

杀掉server

	tmux kill-server

tmux server的socket的默认路径

	/tmp/tmux-$(id -u)/default

通过 tmux -L将使用不同的路径，可以同时启动多个server. 比如

	tmux -L deploy
将使用

	/tmp/tmux-$(id -u)/deploy

也可以直接使用'tmux -S'直接指定socket的全路径

下面问题的解决方法

	$ tmux ls
	failed to connect to server: Connection refused


确认tmux进程存在

	ps -ef | grep tmux

As the process was still running, the issue was a deleted socket, possibly caused by a purged tmp-directory.
According to the tmux mapage:
> If the socket is accidentally removed, the SIGUSR1 signal may be sent to the tmux server process to recreate it.

So sending the signal and attaching works:

	killall -s SIGUSR1 tmux
	tmux attach

save/restore tmux session

	bin/tmux-session save
	bin/tmux-session restore

http://toastdriven.com/blog/2009/oct/09/scripting-tmux/
tmux send-keys ls C-m

所有窗口同时切换到tiger账户

在~/.tmux.conf中添加如下配置

	bind C-e command-prompt "run-shell \"tmux list-windows \| cut -d: -f1\|xargs -I\{\} tmux send-keys -t :\{\} %1\""

然后

	tmux source-file ~/.tmux.conf
	c-b c-e
	# 在调出的run-shell中填写
	b space tiger c-m password c-m
