http://lartc.org/howto/lartc.imq.html
http://lartc.org/howto/
http://linux-ip.net/articles/Traffic-Control-HOWTO/scripts.html
http://edseek.com/~jasonb/articles/traffic_shaping/index.html
http://edseek.com/~jasonb/articles/traffic_shaping/links.html
http://edseek.com/~jasonb/articles/traffic_shaping/scenarios.html
http://linux-ip.net/gl/tcng/
http://sandilands.info/sgordon/dropping-packets-in-ubuntu-linux-using-tc-and-iptables

http://www.cyberciti.biz/faq/linux-traffic-shaping-using-tc-to-control-http-traffic/

monitor band usage

	# iptraf
	# watch /sbin/tc -s -d class show dev eth0


	#!/bin/bash

	RATE=8000
	DEV=eth0

	if [ x$1 = 'xstop' ]
	then
			tc qdisc del dev $DEV root >/dev/null 2>&1
			exit
	fi

	tc filter add dev $DEV parent 1:0 protocol ip u32 match ip sport 80 0xffff classid 1:10
	tc filter add dev $DEV parent 1:0 protocol ip u32 match ip sport 22 0xffff classid 1:20
	tc filter add dev $DEV parent 1:0 protocol ip u32 match ip sport 25 0xffff classid 1:50
	tc filter add dev $DEV parent 1:0 protocol ip u32 match ip sport 110 0xffff classid 1:50

	tc qdisc add dev $DEV root handle 1: htb default 90
	tc class add dev $DEV parent 1: classid 1:1 htb rate ${RATE}kbit ceil ${RATE}kbit

	tc class add dev $DEV parent 1:1 classid 1:10 htb rate 6000kbit ceil ${RATE}kbit
	tc class add dev $DEV parent 1:1 classid 1:20 htb rate 1000kbit ceil ${RATE}kbit
	tc class add dev $DEV parent 1:1 classid 1:50 htb rate 500kbit ceil ${RATE}kbit
	tc class add dev $DEV parent 1:1 classid 1:90 htb rate 500kbit ceil 500kbit

	tc qdisc add dev $DEV parent 1:10 handle 10: sfq perturb 10
	tc qdisc add dev $DEV parent 1:20 handle 20: sfq perturb 10
	tc qdisc add dev $DEV parent 1:50 handle 50: sfq perturb 10
	tc qdisc add dev $DEV parent 1:90 handle 90: sfq perturb 10

http://lartc.org/wondershaper/
http://linux-ip.net/traffic-control/htb-class.png
http://lartc.org/howto/lartc.adv-filter.html
http://lartc.org/howto/lartc.qdisc.filters.html
> with HTB, you should attach all filters to the root!
