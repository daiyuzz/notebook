# 在scrapy中使用django model

### scrapy_djangoitem
直接使用django项目中定义的model作为scrapy的item定义，有scrapy 插件`scrapy_djangoitem`可以使用，其使用如下：
```python
# item.py
import scrapy
from scrapy_djangoitem import DjangoItem
import your_django_app

class SomeModelItem(DjangoItem):
    django_model = your_django_app.models.SomeModel
    # 这里也可以添加其他field
```
注意这里需要import你的django的app，所以需要在scrapy项目中的settings.py中处理好python path，使得运行时可以import相应的module。

### 在scrapy item pipeline中使用django.model存取数据
既然我们处理好python path而且都import 了django的app了，那么下一步自然在item中使用model去存数据了，我们需要在scrapy的settings.py中初始化django：

```python
import django

os.environ['DJANGO_SETTINGS_MODULE'] = 'your_django_app.settings'
django.setup()
```

然后我们可以在pipelines.py中：
```python
from your_crawler.items import *
import your_django_app

class YourCrawlerPipeline(object):
    def process_item(self,item,spider):
        if isinstance(item,SomeModelItem):
            obj,created = your_django_app.models.SomeModel.objects.get_or_create(some_attr=item['some_attr'])
            obj.other_attr = item['other_attr']
            proxy.save()
            return item
```
