### >windows中的一些命令

  
>命令行的详细帮助见http://technet.microsoft.com/en-us/library/cc723564.aspx 下面列出一些常用命令与用法 taskkill: kill进程， 如'taskkill /F /IM notepad.exe' tasklist: list进程，如 'tasklist /FI "IMAGENAME eq notepad.exe"' 单行执行多个命令： cmd1 & cmd2 当上一个命令成功时，执行下一个命令: cmd1 && cmd2 命令行修改Windows防火墙: netsh firewall 以下引自 http://blog.46com.com/article.asp?id=143 `
1.查看当前防火墙配置
netsh firewall show allowedprogram

2.打开远程桌面端口
netsh firewall set portopening TCP 3389 Enable

3.添加后门程序并允许连接网络
假如我们将后门程序植入到了C:\Program Files\Tencent\QQ\QQ.exe
命令：netsh firewall set allowedprogram C:\Program Files\Tencent\QQ\QQ.exe QQ Enable

4.添加特定IP地址到允许范围

比如目标机器已经开放了远程桌面，但我们仍然不能连接，则一般是由于管理员在远程桌面项目中限制了可用连接的范围。通过本命令就可以很容易的突破限制了。

netsh firewall set portopenting TCP 3389 远程桌面　Enable Custom 192.168.0.1
`