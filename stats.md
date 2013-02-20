munin
[graphiti](https://github.com/paperlesspost/graphiti)
	在浏览器上编辑配置
[statsd](https://github.com/etsy/statsd)
[grahpite](http://graphite.wikidot.com/)
[gdash](https://github.com/ripienaar/gdash]
[cubism](http://square.github.com/cubism/)

[graphene](https://github.com/jondot/graphene)

[Measure Anything, Measure Everything](http://codeascraft.etsy.com/2011/02/15/measure-anything-measure-everything/)

[Quick overview of Graphite Dashboard software](http://www.jilles.net/perma/2012/11/01/quick-overview-of-graphite-dashboard-software/)

[Introducing Heka](http://blog.mozilla.org/services/2013/04/30/introducing-heka/)

https://github.com/ClockworkNet/graphite-dashgen

http://graphite.wikidot.com/cli-reference
http://graphite.wikidot.com/cli-tutorial

http://numbers.brighterplanet.com/2012/08/27/graphite-beyond-the-basics/
	介绍如何使用statsd, graphite, graphiti

http://www.aosabook.org/en/graphite.html

statsd python client
[pystatsd](https://github.com/jsocol/pystatsd)
http://statsd.readthedocs.org/en/latest/index.html

	sudo pip install statsd


Install graphite

	sudo pip install django-tagging
	pip install whisper
	pip install carbon
	pip install graphite-web

	# conf carbon
	cd /opt/graphite/conf
	sudo cp carbon.conf.example carbon.conf
	sudo cp storage-schemas.conf.example storage-schemas.conf
	sudo cp dashboard.conf.example dashboard.conf
	
	# start carbon
	sudo /opt/graphite/bin/carbon-cache.py start

	tail -f /opt/graphite/storage/log/carbon-cache/carbon-cache-a/console.log

Feed data

	PORT=2003
	SERVER=dev.btd.com
	echo "local.random.diceroll 4 `date +%s`" | nc ${SERVER} ${PORT};

Test webapp

	sudo /opt/graphite# bin/run-graphite-devel-server.py --port 8081 .

Configure webapp for product use

	sudo pip install gunicorn

	http://dev.btd.com:8081/render?target=carbon.agents.*.*&width=400&height=400


http://www.kinvey.com/blog/89/how-to-set-up-metric-collection-using-graphite-and-statsd-on-ubuntu-1204-lts

# statsd

	cd /opt && sudo git clone git://github.com/etsy/statsd.git
 
	# StatsD configuration

	cat >> /tmp/localConfig.js << EOF
	{
	  graphitePort: 2003
	, graphiteHost: "127.0.0.1"
	, port: 8125
	}
	EOF
	 
	sudo cp /tmp/localConfig.js /opt/statsd/localConfig.js
	cd /opt/statsd && node ./stats.js ./localConfig.js
