http://www.linuxidc.com/Linux/2009-07/20998.htm

nfs server: snake (192.168.1.1)
nfs client: cow (192.168.1.2)

snake

	sudo aptitude install nfs-kernel-server

编辑`/etc/exports`

	/data  cow(rw,all_squash,anonuid=400,anongid=400)
	# 注: all_squash的意思是所有访问用户都被视作用户400:400, 如果不加此选项, 则使用当前访问客户端的uid和gid, 这时，似乎anonuid和anongid就没用了

使配置生效

	# check conf
	sudo exportfs -r
	sudo service nfs-kernel-server restart
	sudo chown himalayas:himalayas /data

cow:

	sudo aptitude install nfs-common
	sudo mkdir -p /mnt/data
	sudo mount snake:/data /mnt/data
