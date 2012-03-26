
安装gem时报错

	$ sudo gem install foo
	WARNING:  RubyGems 1.2+ index not found for:

	RubyGems will revert to legacy indexes degrading performance.
	Bulk updating Gem source index for: http://gems.rubyforge.org/

从源码编译最新gem

	cd /tmp
	curl -O http://production.cf.rubygems.org/rubygems/rubygems-1.8.12.tgz
	tar zxf rubygems-1.8.12.tgz
	cd rubygems-1.8.12
	sudo ruby setup.rb --no-format-executable

重新执行上述命令后, 发现是下面文件不能下载

	http://production.s3.rubygems.org/latest_specs.4.8.gz

更详细信息可使用--debug选项

	sudo gem install foo --debug -V

可看见有时下载类似下述链接也会超时

	http://rubygems.org/quick/Marshal.4.8/rest-client-1.0.4.gemspec.rz


在网关上重新做了route

	 sudo ip route add 207.171.0.0/16 via 10.6.0.2

之后就可以了

使用淘宝镜像

http://ruby-china.org/topics/node3
http://ruby-china.org/topics/577
http://ruby.taobao.org/

或在网关上作dns

110.75.120.11 rubygems.org
110.75.120.11 production.cf.rubygems.org

sudo gem source -r http://rubygems.org/
sudo gem sources -a http://ruby.taobao.org/

另外一个mirror

http://ruby-china.org/topics/856
http://gems.gzruby.org/
