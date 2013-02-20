show raw sql (仅当DEBUG=True时生效)

	from django.db import connection
	print connection.queries

or

	from django.db import connections
	print connections['conn_name'].queries

transaction

	from django.db import transaction

	@transaction.commit_on_success(using='conn_name')
	def foo():
		pass

建表

	DJANGO_SETTINGS_MODULE=ss_datacenter.djangosite.settings django-admin.py sql partner

queryset

	from django.db.models import Q
	User.objects.filter(Q(income__gte=5000) | Q(income=0))

Field lookup

https://docs.djangoproject.com/en/dev/ref/models/querysets/#field-lookups

OneToOneField
ListSeed和ListSeedSchedule 通过id关联 (id为两者的primary key)

	class ListSeed(models.Model):
		pass

	class ListSeedSchedule(models.Model):
		seed = models.OneToOneField('ListSeed', primary_key=True, related_name='schedule', db_column='id')

	# seed.schedule可为None, schedule.seed不为None

不同app间model的复用

[meta options](https://docs.djangoproject.com/en/1.3/ref/models/options/)

app_label
proxy
If proxy = True, a model which subclasses another model will be treated as a [proxy model](https://docs.djangoproject.com/en/1.3/topics/db/models/#proxy-models).

	When using multi-table inheritance, a new database table is created for each subclass of a model. This is usually the desired behavior, since the subclass needs a place to store any additional data fields that are not present on the base class. Sometimes, however, you only want to change the Python behavior of a model – perhaps to change the default manager, or add a new method.

	This is what proxy model inheritance is for: creating a proxy for the original model. You can create, delete and update instances of the proxy model and all the data will be saved as if you were using the original (non-proxied) model. The difference is that you can change things like the default model ordering or the default manager in the proxy, without having to alter the original.

	Proxy models are declared like normal models. You tell Django that it’s a proxy model by setting the proxy attribute of the Meta class to True.

> So, the general rules are:
> 
> If you are mirroring an existing model or database table and don’t want all the original database table columns, use Meta.managed=False. That option is normally useful for modeling database views and tables not under the control of Django.
> If you are wanting to change the Python-only behavior of a model, but keep all the same fields as in the original, use Meta.proxy=True. This sets things up so that the proxy model is an exact copy of the storage structure of the original model when data is saved.

# Cross-database relations
https://docs.djangoproject.com/en/dev/topics/db/multi-db/#limitations-of-multiple-databases

> Django doesn’t currently provide any support for foreign key or many-to-many relationships spanning multiple databases. If you have used a router to partition models to different databases, any foreign key and many-to-many relationships defined by those models must be internal to a single database.

# form
https://docs.djangoproject.com/en/1.5/#forms

# save

	class ArticleItem(BaseArticleItem):
		group = models.ForeignKey('ArticleGroup', null=True, blank=True, related_name='items')

两种方法

	a = ArticleItem(group=ArticleGroup.objects.get(pk=group_id))
	a.save()
或(未验证)

	a = ArticleItem(group_id=group_id)
	a.save()
	

django-and-large-datasets

mysqldb和django会对结果集进行cache, 大数据集时导致占用大量内存, 使用iterator也不管用

	http://hustoknow.blogspot.jp/2011/07/django-and-large-datasets.html
	http://thebuild.com/blog/2010/12/13/very-large-result-sets-in-django-using-postgresql/

> Don’t use Django to manage queries that have very large result sets. If you must, be sure you understand how to keep memory usage manageable.

> When a database query is executed, the Psycopg cursor usually fetches all the records returned by the backend, transferring them to the client process. If the query returned an huge amount of data, a proportionally large amount of memory will be allocated by the client.

# validators

https://docs.djangoproject.com/en/dev/ref/forms/validation/

- field定义时使用validators
- 或Model中定义clean方法


	from django.core.exceptions import ValidationError

	def validate_domain(domain):
		if not is_valid_domain(domain):
			raise ValidationError(u'域名格式错误')

	class ArticleDomain(models.Model):
		domain = models.CharField(max_length=127, unique=True, verbose_name=u'域名', validators=[validate_domain])
		def clean(self):
			if self.auth_status == ArticleAuthStatus.never and self.display_type != ArticleDisplayType.mobile:
				raise ValidationError(u'不授权站点的展示类型必须为mobile')
