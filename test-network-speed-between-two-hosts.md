准备

	sudo aptitude install iperf

On host1

	iperf -s

On host2

	iperf -c host1 -d

Example:

On host front2 (192.168.0.32)

	iperf -s
	------------------------------------------------------------
	Server listening on TCP port 5001
	TCP window size: 85.3 KByte (default)
	------------------------------------------------------------

On host front1 (192.168.0.31)

	iperf -c front2 -d
	------------------------------------------------------------
	Server listening on TCP port 5001
	TCP window size: 85.3 KByte (default)
	------------------------------------------------------------
	------------------------------------------------------------
	Client connecting to front2, TCP port 5001
	TCP window size:   181 KByte (default)
	------------------------------------------------------------
	[  5] local 192.168.0.31 port 35940 connected with 192.168.0.32 port 5001
	[  4] local 192.168.0.31 port 5001 connected with 192.168.0.32 port 36843
	[ ID] Interval       Transfer     Bandwidth
	[  5]  0.0-10.0 sec  1.05 GBytes    904 Mbits/sec
	[  4]  0.0-10.0 sec    992 MBytes    831 Mbits/sec

上面的`-d`参数表示双向速度测试，因此front2也显示网络速度信息，然后退出

	[  4] local 192.168.0.32 port 5001 connected with 192.168.0.31 port 35940
	------------------------------------------------------------
	Client connecting to 192.168.0.31, TCP port 5001
	TCP window size:   259 KByte (default)
	------------------------------------------------------------
	[  6] local 192.168.0.32 port 36843 connected with 192.168.0.31 port 5001
	Waiting for server threads to complete. Interrupt again to force quit.
	[ ID] Interval       Transfer     Bandwidth
	[  6]  0.0-10.0 sec    992 MBytes    832 Mbits/sec
	[  4]  0.0-10.0 sec  1.05 GBytes    903 Mbits/sec

Reference

- http://askubuntu.com/questions/7976/how-do-you-test-the-network-speed-betwen-two-boxes
