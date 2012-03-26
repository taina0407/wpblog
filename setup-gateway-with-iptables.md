本文介绍使用iptables搭建网关。

# 环境

外网 - 211.103.252.242/28
内网1 - 10.6.0.1/16
内网2 - 10.9.0.1/16

# 配置

OS - ubuntu 10.04 server 32bit
cpu - Pentium(R) Dual-Core E5400  @ 2.70GHz
内存 - 2GB

3个网络接口, eth0接电信通10M光纤, eth1和eth2分别接6楼和9楼两个局域网。

配置 `/etc/network/interfaces`如下

	auto lo
	iface lo inet loopback

	# wan
	auto eth0
	iface eth0 inet static
	address 211.103.252.242
	netmask 255.255.255.240
	gateway 211.103.252.241

	# lan 1 6th floor
	auto eth1
	iface eth1 inet static
	address 10.6.0.1
	netmask 255.255.0.0

	# lan 2 9th floor
	auto eth2
	iface eth2 inet static
	address 10.9.0.1
	netmask 255.255.0.0

# 配置dns和dhcp服务

	sudo aptitude install dnsmasq

编辑`/etc/dnsmasq.d/local.conf`

	# dhcp
	expand-hosts
	domain=eee168.com
	dhcp-range=eth1, 10.6.1.1, 10.6.3.255, 255.255.0.0, 24h
	dhcp-range=eth2, 10.9.1.1, 10.9.3.255, 255.255.0.0, 24h
	read-ethers
	dhcp-hostsfile=/etc/dhcp-hosts

	# dns
	dhcp-option=6,10.6.0.1

	# for pxe boot
	enable-tftp
	tftp-root=/tftpboot
	dhcp-boot=pxelinux.0

修改了配置或/etc/hosts, 可通过下面命令重新载入配置

	sudo killall -s HUP dnsmasq

# 配置防火墙

允许转发, 添加文件 `/etc/sysctl.d/60-ip-forward.conf`

	net.ipv4.ip_forward=1

使配置生效

	sudo service procps restart
	sudo sysctl net.ipv4.ip_forward

配置`firewall.conf`, 通过执行`sudo iptables-restore < firewall.conf`使配置生效

	*filter

	# less than 100 concurrent connections per ip
	-A FORWARD -p tcp --syn -m connlimit --connlimit-above 200 -j LOG --log-prefix "ipt.connlimit:"
	-A FORWARD -p tcp --syn -m connlimit --connlimit-above 200 -j DROP

	# -m limit will treat all lan hosts in a rule as a whole, while -m hashlimit can treat each host separately.

	# less than 300 * 1.5KB/s for each lan host to download (wan -> lan)
	-A FORWARD -i eth0 -m hashlimit --hashlimit-name dratelimit --hashlimit-mode dstip --hashlimit-burst 100 --hashlimit-above 300/sec -j DROP

	# less than 400 * 1.5KB/s for each lan host to upload (lan -> wan)
	-A FORWARD -o eth0 -m hashlimit --hashlimit-name uratelimit --hashlimit-mode srcip --hashlimit-burst 100 --hashlimit-above 400/sec -j DROP

	# less than 30 new packets per second from each lan host
	# can not use -s 10.0.0.8, when from 10.6.0.12 -> 10.61.0.115 (in and out are both eth1), all tcp packets seems to be NEW, so speed is limited to 30 * 1.5 KB/s
	# -A FORWARD -s 10.0.0.8 -m state --state NEW -m hashlimit --hashlimit-name deflood --hashlimit-mode srcip --hashlimit-above 20/sec --hashlimit-burst 20 -j DROP
	-A FORWARD -o eth0 -m state --state NEW -m hashlimit --hashlimit-name deflood --hashlimit-mode srcip --hashlimit-above 30/sec --hashlimit-burst 50 -j LOG --log-prefix "ipt.deflood:"
	-A FORWARD -o eth0 -m state --state NEW -m hashlimit --hashlimit-name deflood --hashlimit-mode srcip --hashlimit-above 30/sec --hashlimit-burst 50 -j DROP

	COMMIT

	*nat
	:PREROUTING ACCEPT [19189:1396308]
	:POSTROUTING ACCEPT [5110:330966]
	:OUTPUT ACCEPT [4073:277813]
	-A PREROUTING -d 211.103.252.242/32 -p tcp -m tcp --dport 80 -j DNAT --to-destination 10.6.0.11 
	-A POSTROUTING -s 10.0.0.0/8 -o eth0 -j SNAT --to-source 211.103.252.242
	COMMIT

# 配置路由

	ip route add 74.125.0.0/16 via 10.6.0.2 # gmail
	ip route add 10.92.0.0/16 via 10.9.0.92


connect: No buffer space available

	it may be due to wrong routing table on your system.
	connection may be looped because of this.

	check with # route -n

ipv4: Neighbour table overflow

The neighbour table is generally known as ARP table

	net.ipv4.neigh.default.gc_thresh1 = 4096
	net.ipv4.neigh.default.gc_thresh2 = 8192
	net.ipv4.neigh.default.gc_thresh3 = 8192
	net.ipv4.neigh.default.base_reachable_time = 86400
	net.ipv4.neigh.default.gc_stale_time = 86400

http://www.serveradminblog.com/2011/02/neighbour-table-overflow-sysctl-conf-tunning/
