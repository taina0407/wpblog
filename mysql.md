

cow,snake为mysql master master replication. cow重启，导致cow和snake的同步都停止的原因

snake:

	show slave stauts \G
              Master_Log_File: mysql-bin.000562
          Read_Master_Log_Pos: 18045830
               Relay_Log_File: mysql-relay-bin.000007
                Relay_Log_Pos: 251
        Relay_Master_Log_File: mysql-bin.000562
          Exec_Master_Log_Pos: 18045830

	100118 10:05:56 [ERROR] Got fatal error 1236: 'Client requested master to start replication from impossible position' from master when reading data from binary log

查看日志`/var/log/mysql/error.log`

	110715 15:48:19 [ERROR] Error reading packet from server: Client requested master to start replication from impossible position ( server_errno=1236)
	110715 15:48:19 [ERROR] Slave I/O: Got fatal error 1236 from master when reading data from binary log: 'Client requested master to start replication from impossible pos
	ition', Error_code: 1236
	110715 15:48:19 [Note] Slave I/O thread exiting, read up to log 'mysql-bin.000562', position 18045830

原因 cow的一部分binlog没来得及保存，但已经传到了snake，导致snake的Read_Master_Log_Pos advances to impossible position

解决方法

	change master to MASTER_LOG_FILE='NEXT BIN LOG FILE', MASTER_LOG_POS=0

example:

	stop slave;
	change master to MASTER_LOG_FILE='mysql-bin.000563',  MASTER_LOG_POS=0;
	start slave;


cow:

	show slave stauts \G
	1062, duplicated key

原因: snake的一部分数据已经同步到了cow, 但cow重启导致relay-log.info (记录了Slave_SQL thread执行到的位置)部分信息没保存。重启后，从之前某个位置开始重新执行，导致部分数据被重新apply，从而key 冲突。

解决办法： 跳过此部分数据，方法如下

	mk-slave-restart -uroot --error-numbers 1062 h=cow


# master-master 配置

http://mysql-mmm.org/mmm2:guide
http://dev.mysql.com/tech-resources/articles/advanced-mysql-replication.html

server 1 (server-id=1)

	auto_increment_increment = 2
	auto_increment_offset = 1

server 2 (server-id=2)

	auto_increment_increment = 2
	auto_increment_offset = 2

mysql replication

master server config

	[mysqld]
	log-bin=mysql-bin
	server-id=1

slave server

	[mysqld]
	server-id=2
	relay-log=mysqld-relay-bin

restart master and slave

config user for replication on master

	GRANT REPLICATION SLAVE  ON *.* TO 'replication'@'192.168.1.%' IDENTIFIED BY 'letou123#*';
	FLUSH TABLES WITH READ LOCK;

In a different session on the master

	mysql > SHOW MASTER STATUS;
	+------------------+----------+--------------+------------------+
	| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
	+------------------+----------+--------------+------------------+
	| mysql-bin.000003 | 73       | test         | manual,mysql     |
	+------------------+----------+--------------+------------------+

If the master has been running previously without binary logging enabled, the log file name and position values displayed by SHOW MASTER STATUS or mysqldump --master-data will be empty. In that case, the values that you need to use later when specifying the slave's log file and position are the empty string ('') and 4.

dump the master data

	mysqldump --all-databases --lock-all-tables > /tmp/database-backup.sql

release the read lock (close the session)

If you need start your slave with a fresh copy of the master database, you will need to issue the commands 
STOP SLAVE and RESET SLAVE before you do a CHANGE MASTER to give it the new file name and position (and before you restart the slave!)
Otherwise, it tries to pick up where it left off, using the old master file and position.

# debug master slave

SHOW PROCESSLIST

The relay log consists of the events read from the binary log of the master and written by the slave I/O thread. Events in the relay log are executed on the slave as part of the SQL thread.

The master info log contains status and current configuration information for the slave's connection to the master. This log holds information on the master host name, login credentials, and coordinates indicating how far the slave has read from the master's binary log.

The relay log info log holds status information about the execution point within the slave's relay log.

 Failed to open the relay log and Could not find target log during relay log initialization. 
If you encounter the issue after replication has already begun, one way to work around it is to stop the slave server, prepend the contents of the old relay log index file to the new one, and then restart the slave. On a Unix system, this can be done as shown here:

	shell> cat new_relay_log_name.index >> old_relay_log_name.index
	shell> mv old_relay_log_name.index new_relay_log_name.index

The SQL thread automatically deletes each relay log file as soon as it has executed all events in the file and no longer needs it. There is no explicit mechanism for deleting relay logs because the SQL thread takes care of doing so. 

http://dev.mysql.com/doc/refman/5.1/en/slave-logs-status.html
 master.info, relay-log.info: The next time the slave starts up, it reads the two logs to determine how far it has proceeded in reading binary logs from the master and in processing its own relay logs.

Line in relay-log.info	SHOW SLAVE STATUS Column	Description
1	Relay_Log_File	The name of the current relay log file
2	Relay_Log_Pos	The current position within the relay log file; events up to this position have been executed on the slave database
3	Relay_Master_Log_File	The name of the master binary log file from which the events in the relay log file were read
http://serverfault.com/questions/103729/mysql-replication-issues-after-a-power-outage

# too long key

InnoDB若使用`varchar(256)`作为key, 则会出现如下错误

	Specified key was too long; max key length is 767 bytes

修改方法: `256 * 3 = 768 > 767`, 改为 `varchar(255)`即可.


