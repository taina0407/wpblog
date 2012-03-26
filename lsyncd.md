lsyncd - backup/mirror with inotify and rsync

install on ubuntu server 10.04

wget http://lsyncd.googlecode.com/files/lsyncd-2.0.2.tar.gz
cd lsyncd-2.02
sudo aptitude install lua5.1 liblua5.1-0-dev
./configure
make
sudo make install


www-data@snake$ cat > sync.lua <<EOF
settings = {
        nodamon = false,
}
sync{default.rsyncssh, source="/data/wowpad/releases/update/", host="himalayas@cow", targetdir="/data/wowpad/releases/update/"}
EOF


www-data@snake$ lsyncd sync.lua
tail -f /var/log/messages


lsyncd有些目录不能sync的原因

inotify事件的应用

在应用中遇到了一个问题，从日志中发现 error: Cannot add watch xxx (28:No space left on device) 这样的提示， 检查了一下磁盘空间还很充足，文件系统使用的reiserfs，inode就不考虑了，查了一下inotify手册发现在内核参数中是有设置最大监控对象数量的 cat /proc/sys/fs/inotify/max_user_watches 只有很小的一个默认数。修改/etc/sysctl.conf 加入 fs.inotify.max_user_watches = 262144 具体数根据子目录的大小来决定，如果此值小于需要被监控的子目录数，那么某些子目录会没有被监控，同步自然会出问题。加入后"service procps start"并重新启动lsyncd进程监控初始化。

Inotify: 高效、实时的Linux文件系统事件监控框架

cat /etc/sysctl.d/60-inotify.conf 

	# two instance of lsyncd will watch 2 * 65535 directories
	fs.inotify.max_user_watches = 4000000

使配置生效

	sudo service procps start

查看当前配置

	sudo sysctl fs.inotify.max_user_watches
