http://munin-monitoring.org/wiki/HowToWritePlugins

[Error handling in plugins](http://munin-monitoring.org/wiki/HowToWritePlugins)
	please both give an error message to stderr and exit with a non-zero value

http://munin-monitoring.org/wiki/ConcisePlugins

A plugin is invoked twice every update-cycle. Once to emit config information and once to fetch values

测试munin

	nc 192.168.20.44 4949
	fetch article_alarm_py
	config article_alarm_py


