# 创建raid1

	sudo mdadm --create /dev/md0 --metadata 1.2 -l 1 -n 2 /dev/sdb1 /dev/sdc1

# 将raid配置保存到系统配置以便系统重启时也生效.

将下面命令

	sudo mdadm --detail --scan

类似如下的输出加到/etc/mdadm/mdadm.conf末尾

	ARRAY /dev/md1 level=raid1 num-devices=2 metadata=01.02 name=data1:1 UUID=e7e2f8b2:f2e27467:c4a8f55d:3541f85f
	ARRAY /dev/md0 level=raid1 num-devices=2 metadata=01.02 name=data1:0 UUID=f32ef209:156a211a:b8ada163:22191658

# 检查raid状态

	sudo mdadm --detail /dev/md0
	sudo mdadm --examine /dev/sdb1
	cat /proc/mdstat

# 将新发现的设备加入raid array

此设备必须原来确实属于某个raid，不能将新的设备以此种方法加入

	sudo mdadm --incremental /dev/sdb1

# 管理

	sudo mdadm /dev/md0 -f /dev/sdb1 -r /dev/sdb1 -a /dev/sdb1

will firstly mark /dev/sdb1 as faulty in /dev/md0 and will then remove it from the array and finally add it back in as a spare

# 系统重启后raid故障修复

如果没有按上面的步骤修改/etc/mdadm/mdadm.conf, 则系统启动时raid可能不正常。修复方法如下

## 方法1

找到md的uuid

	sudo mdadm --examine /dev/sdb1

修改/etc/mdadm/mdadm.conf, 在末尾自行添加ARRAY配置, 然后

	sudo mdadm --assemble --scan

## 方法2

人工装配, 类似如下命令 (未测试过)

	sudo mdadm --assemble /dev/md0 /dev/sdb1 /dev/sdc1

然后将raid配置保存到/etc/mdstat/mdstat (见上面的步骤)
