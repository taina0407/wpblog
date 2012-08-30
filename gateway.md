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
