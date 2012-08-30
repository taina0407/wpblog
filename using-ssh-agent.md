"=========== Meta ============
"StrID : 245
"Title : 使用SSH Agent Forwarding
"Slug  : using-ssh-agent-forwarding
"Cats  : Uncategorized
"Tags  : ssh, ssh-agent
"=============================
"EditType   : post
"EditFormat : Markdown
"BlogAddr   : http://blog.pkufranky.com/
"========== Content ==========
$TOC$

[SSH Agent Forwarding原理][ssh-agent-forwarding-guide]讲了ssh认证以及agent forwarding的基本原理， 但没有讲具体该怎么做。下面就讲讲最佳实践 (Best Practice). [Using ssh-agent with ssh][]一文讲得很清楚，这里做一下翻译和扩展。
<!--more-->

# 公钥认证配置

1.  在本机生成公私钥对

		:::bash
		ssh-keygen -f $HOME/.ssh/id_rsa -C "you@example.com"

	会提示你输入passphrase, 请务必要输入passphrase, 不然有很大安全风险。另外请把命令中的email换成你自己的真实email.

	这样会在`$HOME/.ssh`下生成`id_rsa`和`id_rsa.pub`两个文件。前者是你的私钥(身份标识)，请不要泄露给任何人，后者是公钥，可随意分发，下面会讲到如何使用。原理部分已经提到，其实`id_rsa.pub`中的内容可通过`ssh-keygen -y $HOME/.ssh/id_rsa`提取出来。单独生成一个文件只是为了方便之后使用。

	`id_rsa`可以作为你的身份标识，当你更换机器或重装系统的时候，请记得备份并随后恢复这两个文件，而不要再重新生成。如果你有多台机器，也建议使用同一份`id_rsa`.

2.  配置远程机器的`$HOME/.ssh/authorized_keys`以允许公钥认证

	假设你想从本机`homepc`通过公钥以用户`yourname`的身份登陆远程机器`box`，则需要将你的公钥添加到`yourname@box`(表示box机器的用户`yourname`)的`$HOME/.ssh/authorized_keys`中。如果你能够以密码认证的方式登陆`yourname@box`, 则可在`homepc`执行如下命令

		:::bash
		cat ~/.ssh/id_dsa.pub | ssh yourname@box 'cat - >> ~/.ssh/authorized_keys'

	或使用更简单的命令

		:::bash
		ssh-copy-id yourname@box

	如果你没有权限访问此机器，则可以请系统管理员代你进行操作。

3.  验证公钥认证是否工作

	从本机登陆`yourname@box`，这时会提示你输入passphrase, 如果输入之后能成功登陆，那证明配置成功了。

		:::bash
		$ ssh yourname@box
		Enter passphrase for RSA key 'you@example.com':

	如果上述步骤不成功，请检查配置是否正确

		:::bash
		$ ls -lah $HOME/.ssh
		drwx------  3 ping ping 4.0K 2012-08-29 18:08 .
		-rw-------  1 ping ping  314 2010-08-27 00:56 id_rsa

	主要确认.ssh/id_rsa存在且.ssh和.ssh/id_rsa的owner和权限正确，即owner必须为当前用户且只能当前用户可读写，这样以防止私钥被其他用户访问。

	如果不正确，可通过如下命令纠正

		:::bash
		sudo chown -R $USER:$USER .ssh # 仅当owner不正确时执行此步
		chmod 700 .ssh
		chmod 600 .ssh/id_rsa

配置成功后，想要无passphrase登陆？下面就讲如何配置ssh agent以及agent forwarding.

# agent forwarding配置

agent原理最重要的一点，就是`$SSH_AUTH_SOCK`环境变量，只要这个环境变量存在, ssh client就会通过此变量指向的域套接字 (domain socket)和agent通信从而实现私钥通过agent管理。所以配置时注意两点就可以了

1. ssh-agent已经启动，且私钥已纳入了管理
2. $SSH_AUTH_SOCK环境变量被正确设置

做法有两种，一种是将需要程序作为ssh-agent的子进程启动，比如

	:::bash
	ssh-agent gnome-session

这种本文不多讲，请参考[Using ssh-agent with ssh][]

另一种是终端/console模式下最常用的, 在shell中设置环境变量`$SSH_AUTH_SOCK`，所有在shell中执行的命令自动继承此环境变量。

将如下代码加到本机的`$HOME/.bashrc`中

	:::bash
	SSH_ENV="$HOME/.ssh/environment"
	function start_agent {
	  echo "Initialising new SSH agent..."
	  /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
	  echo succeeded
	  chmod 600 "${SSH_ENV}"
	  . "${SSH_ENV}" > /dev/null
	  /usr/bin/ssh-add;
	}

	# Source SSH settings, if applicable

	if [ -f "${SSH_ENV}" ]; then
	  . "${SSH_ENV}" > /dev/null
	  #ps ${SSH_AGENT_PID} doesn’t work under cywgin
	  ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
	  start_agent;
	}
	else
	  start_agent;
	fi

其作用是新开一个终端时, 先检测agent是否启动，如果已启动，则通过`source $HOME/.ssh/environment`给当前shell设置环境变量。该文件内容类似下面

	:::bash
	SSH_AUTH_SOCK=/tmp/ssh-cBdruoAnh5/agent.5576; export SSH_AUTH_SOCK;
	SSH_AGENT_PID=5578; export SSH_AGENT_PID;

如果未启动，则启动agent, 加入私钥，然后保存环境变量到`$HOME/.ssh/environment`并在当前shell设置此环境变量。

因此，第一次启动终端时，会提示你输入passphrase。但之后再启动新的终端时或在已有终端里进行ssh相关操作时，则不会再提示输入passphrase了。

注意上述`.bashrc`的改动仅限于私钥所在的机器(这里是本地机器homepc)，因为只有在此机器才有必要启动ssh-agent。在登陆的远端机器(server)上是不需要启动ssh-agent的，因此对该远端机器的.bashrc不要做此改动。

但如果再想从此远端机器(server)无密码登陆到第三台机器(server2)，server上也是需要`$SSH_AUTH_SOCK`环境变量的。只要在ssh登陆时加上了`-A`参数打开agent forwarding，则登陆成功后会自动在目标机器的shell中设置此环境变量。

下面的例子, homepc上设置好了ssh-agent, 只要路径上一直保持打开agent forwarding，所有随后的级联登陆都不需要输入passphrase.

	:::bash
	yourname@homepc:~$ echo $SSH_AUTH_SOCK # ssh client会通过它和homepc的ssh-agent通信
	/tmp/ssh-UNJHj21949/agent.21949
	yourname@homepc:~$ ssh -A server
	yourname@server:~$ ssh -A server2
	yourname@server2:~$ ssh -A server3
	yourname@server3:~$ echo $SSH_AUTH_SOCK # ssh client会通过它和server3的sshd通信, server3并没有启动ssh-agent
	/tmp/ssh-MhVpVs1071/agent.1071

# windows securecrt 配置

如果工作机器是windows，则登陆到远端机器工作最方便的terminal是securecrt。在securecrt上配置公钥认证登陆和打开agent forwarding所做的底层工作其实是一样的，只是配置方式不同而已(需通过GUI来配置)。具体需要配置的地方如下

1. 选项 > 全局选项 > SSH2
	- 使用身份或证书中 配置私钥(id_rsa)的路径
	- 高级中选中 添加密要到代理程序和启用OpenSSH代理程序转发

	![配置私钥路径和打开agent forwarding](http://pic.yupoo.com/pkufranky/Ceg4OeD6/GBeIq.jpg)

2. 新建的连接属性 > 鉴权, 将公钥选项选中并设置为第一个选项

	![配置优先公钥认证](http://pic.yupoo.com/pkufranky/Ceg4NJTG/o2noh.jpg)


[Using ssh-agent with ssh]: http://mah.everybody.org/docs/ssh
[ssh-agent-forwarding-guide]: /2012/08/ssh-agent-forwarding-guide/
