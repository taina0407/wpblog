
datacenter
	in djangosite directory

	python manage.py create_permissions app_name

source ~/repos/ss_extractor/rc/bytedrc

访问服务器(拥有sudo权限), 在开发机

	b OPAGENT
	g 42
	sudo ls

~/todo-log
	article_category.log
		分类卡住了，需要分析原因
		增加自动检测卡住并重启机制

统计每个domain的文章数， 文章数倒序

	select SUBSTRING(url from 1 for locate('/',url ,10)-1) as domain, count(*) as cnt
		from article_tag as T left join article_group as G on (group_id = G.id)
		where recommend_time > '2013-05-06' and recommend_status in (17, 18)
		group by domain
		having cnt > 5 order by cnt desc;

线上测试datacenter

	python manage.py runserver 0.0.0.0:4120 --settings=settings_test

django manage.py

	python manage.py sqlall article_rules
	python manage.py -h

ss_datacenter, ss_site 支持pushonline -m admin 重新相应uwsgi
ss_op
	pushonline -s0 -m svc_<svcname> 重启某个svc服务 (只支持ss_op)
	pushonline -s0 -m svc_uwsgi_op_admin_run
ss_crawler
	tiger用户 /opt/tiger/ss_crawler
	bash restart-crawler.sh

django ajax CSRF verification failed. Request aborted.

	$.ajaxSetup({
		 beforeSend: function(xhr, settings) {
			 function getCookie(name) {
				 var cookieValue = null;
				 if (document.cookie && document.cookie != '') {
					 var cookies = document.cookie.split(';');
					 for (var i = 0; i < cookies.length; i++) {
						 var cookie = jQuery.trim(cookies[i]);
						 // Does this cookie string begin with the name we want?
						 if (cookie.substring(0, name.length + 1) == (name + '=')) {
							 cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
							 break;
						 }
					 }
				 }
				 return cookieValue;
			 }
			 if (!(/^http:.*/.test(settings.url) || /^https:.*/.test(settings.url))) {
				 // Only send the token to relative URLs i.e. locally.
				 xhr.setRequestHeader("X-CSRFToken", getCookie('csrftoken'));
			 }
		 }
	});

image_group
image_bin

similar_search报警处理
通过svc日志找到对应机器, 进/opt/tiger/image_bin/data<n>/, 

	mv word_index.log word_index.<now>

然后重启

	sudo svc -h ss_search_<n>_run/

日志 image_bin/logs

	mysql -h10.4.16.23 -uuser -ppassword ss -e "select T.id, G.title,I.publish_time, I.url  from article_tag as T left join article_group as G ON (T.group_id = G.id) left join article_item as I ON (G.key_item_id = I.id) where T.create_time > '2013-08-18' and I.publish_time = I.fetch_time and recommend_status=1 order by I.url;" > fallback-pubtime.txt

添加一个新的essay tag的步骤

	http://admin.btd.com/crawler/admin/message/label/
	ss_datacenter.djangosite.essay.types.EssayTagEnum
	ss_datacenter.djangosite.essay.models.TAGS
	重启datacenter和essay_sethot

查看文章评论数

	select sum(S.messages_count), sum(S.reposts_count), sum(S.comments_count), sum(S.fetched_comments_count), count(G.id) from article_group as G left join article_stats AS S ON (G.id = S.group_id) where G.fetch_time > '2013-09-15';

监控数据库ss的sql
在10.4.16.23

	sudo /opt/tools/mysql-sniffer -ieth0 -f 'tcp and port 3306 and dst host 10.4.16.23' | grep article

查看服务状态

for h in 119 120 144 145 146 159 160 161; do ssh 10.4.16.$h 'hostname -i; cd /etc/service; sudo svstat * | sed "s/^/  /g"'; done

tmux中

	c-b c-e
	source space /mnt/mfs/share/bytedrc c-m
