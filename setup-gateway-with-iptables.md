"=========== Meta ============
"StrID : 173
"Title : ubuntu服务器下使用iptables搭建双局域网NAT网关
"Slug  : setup-dual-lan-nat-gateway-in-ubuntu-with-iptables
"Cats  : Uncategorized
"Tags  : gateway, iptables
"=============================
"EditType   : post
"EditFormat : Markdown
"BlogAddr   : http://blog.pkufranky.com/
"========== Content ==========
本文介绍在ubuntu下使用iptables搭建NAT双局域网网关。

# 目标环境

- 外网 - 211.103.252.242/28
- 内网1 (6楼) - 10.6.0.1/16
- 内网2 (9楼) - 10.9.0.1/16

所有局域网机器通过dhcp自动分配ip。两个局域网的机器间可以互相访问。

# 网关服务器配置

* OS - ubuntu 10.04 server 32bit
* cpu - Pentium(R) Dual-Core E5400  @ 2.70GHz
* 内存 - 2GB
* 3个百兆网络接口 - 1个主板内置接口和2个pci网卡

<!--more--> 

# 配置网络

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

`/etc/dhcp-hosts`可为mac指定固定ip, 文件示例

	6C:F0:49:B1:08:D1,bear,10.6.0.11
	E0:CB:4E:CD:A4:4C,dog,10.6.0.12

dnsmasq在做dns解析时, 会先从`/etc/hosts`中读取域名和ip的映射, 如果没找到，才会将请求forward到`/etc/resolv.conf`中的dns服务器。因此可修改网关的`/etc/hosts`来重载在局域网内对某域名的解析。dnsmasq也会将通过其dhcp服务获取ip的机器的hostname自动解析成ip, 因此在局域网中, 可直接通过机器的hostname访问到对应机器。

本网关的`/etc/resolv.conf`如下

	nameserver 10.6.0.1
	nameserver 203.196.0.6
	nameserver 8.8.8.8
	nameserver 202.106.196.115
	domain eee168.com
	search eee168.com

修改了配置或/etc/hosts, 可通过下面命令重新载入配置

	:::bash
	sudo killall -s HUP dnsmasq

或

	:::bash
	sudo service dnsmasq reload


# 配置NAT和防火墙

允许转发, 添加文件 `/etc/sysctl.d/60-ip-forward.conf`

	net.ipv4.ip_forward=1

如果没有这个配置，可能局域网的机器都无法上外网。

使配置生效

	:::bash
	sudo service procps restart
	sudo sysctl net.ipv4.ip_forward

然后配置`firewall.conf`, 以

- 限制单ip的链接数和上传下载速度。
- 将网关80端口导向内网机器10.6.0.11
- 配置NAT: SNAT地址转换以使内网机器能访问外网

通过执行`sudo iptables-restore < firewall.conf`使配置生效

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

