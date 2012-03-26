# eth0和eth1互换

编辑`/etc/udev/rules.d/70-persistent-net.rules`, 将eth0和eth1的值交换即可

# ubuntu决定eth0/1顺序的原则
似乎是根据mac地址排序

# master-master更改两个服务器ip

db1和db2的ip由192.168.1.1/2改为192.168.0.11/12，下面给出在db1上执行的命令，db2类似，就不写了


首先更改权限

	GRANT REPLICATION CLIENT                 ON *.* TO 'mmm_monitor'@'192.168.0.%' IDENTIFIED BY 'password';
	GRANT SUPER, REPLICATION CLIENT, PROCESS ON *.* TO 'mmm_agent'@'192.168.0.%'   IDENTIFIED BY 'password';
	GRANT REPLICATION SLAVE                  ON *.* TO 'replication'@'192.168.0.%' IDENTIFIED BY 'password';
	GRANT ALL ON *.* TO 'root'@'192.168.0.%';

然后`show slave status \G`，结果如下

				 Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000743
          Read_Master_Log_Pos: 7415
               Relay_Log_File: mysql-relay-bin.000750
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000743
		...
          Exec_Master_Log_Pos: 7415

根据上面的数据，执行

	stop slave;
	change master to MASTER_HOST='192.168.0.12',MASTER_LOG_FILE='mysql-bin.000743', MASTER_LOG_POS=7415;
	start slave;

如果在配置时使用hostname代替ip，如`db2`代替`192.168.0.12`，则改ip后，只要更改/etc/hosts文件，不同更改mysql配置。

# apache2控制带宽

安装 mod_bw module

	sudo aptitude install libapache2-mod-bw
	sudo a2enmod bw

限速配置 (可放到virtual host里)

    BandWidthModule On
    ForceBandWidthModule On
    # 2MB/s in total for all clients
    BandWidth all 2048000
    # each connection has at least 50KB/s
    MinBandwidth all 51200
    # at most 200KB/s for files larger than 1M
    LargeFileLimit * 1024 204800

重启服务

	sudo service apache2 reload

配置官方文档见 http://bwmod.sourceforge.net/files/mod_bw-0.7.txt

更好的限速模块是 [mod_cband](http://dembol.org/mod_cband/), 但ubuntu 10.04中没有此模块，还得编译，因此选用了mod_bw。见 [Apache rate limiting options](http://stackoverflow.com/questions/131681/apache-rate-limiting-options)

http://www.uno-code.com/?q=node/64

ubuntu 10.04 安装mod_cband

安装gcc和apxs2

	apt-get install gcc
	apt-get install apache2-threaded-dev

change in the Makefile.in:

	Replace "APXS_OPTS=-Wc,-Wall -Wc,-DDST_CLASS=3"
	with: "APXS_OPTS=-lm -Wc,-Wall -Wc,-DDST_CLASS=3"

Then:

	./configure
	make && make install

http://pessetto.com/question2answer/index.php?qa=3&qa_1=why-does-mod_cband-have-a-truncf-symbol-undefined
http://biztos.blogspot.com/2008/02/installing-apxs2-on-ubuntu-server.html

# add swap

	mkswap /dev/hda3
	swapon /dev/hda3
http://www.cyberciti.biz/faq/linux-add-a-swap-file-howto/

# apache rewrite post request

	RewriteRule ^applog/report http://api.wowpad.cn/applog/report [R=301,P,L]

不足是由于是服务器端代理转发，最后日志中的ip为服务器ip而非客户端ip

# mysql performance

http://www.howtogeek.com/howto/programming/speed-up-your-web-site-with-mysql-query-caching/

# restart network in ubuntu

To restart netwokring from remote probably the best and securest approach will be run the following within a screen session:

	/etc/init.d/networking stop; /etc/init.d/networking start

/var/log/messages has been deleted from Natty.
You can find the same info in /var/log/syslog. Note that everything logged to messages was also logged to syslog.
