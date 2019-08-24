## 使用scrapy-redis实现分布式爬虫示例

### master端

###### 1.修改master的redis配置文件redis.conf:

​	将bind 127.0.0.1 修改为bind 0.0.0.0    (注意关闭防火墙)

​	重启redis-server

```python
# -*- coding: utf-8 -*-
import scrapy
from scrapy_redis.spiders import RedisSpider
from Newsspider.items import NewsspiderItem

class NewsSpider(RedisSpider):
    name = 'news'
    #启动爬虫命令
    redis_key = 'newsspider:start_urls'

    #动态定义爬虫爬取范围
    def __init__(self,*args,**kwargs):
        domain = kwargs.pop('domain','news.qq.com')
        self.allowed_domains = filter(None,domain.split(','))
        super(NewsSpider,self).__init__(*args,**kwargs)



    def parse(self, response):
        # print(response.xpath('//title/text()'))
        Mnews = response.xpath('//div[@class="Q-tpList"]/div/div/em')
        for news in Mnews:
            # print(news)
            item = NewsspiderItem()
            name = news.xpath('.//a/text()').extract()
            link = news.xpath('.//a/@href').extract()
            item['name'] = name
            item['link'] = link
            # print(name)
            yield item
```



```python
#settings.py
REDIS_HOST = 'localhost'
REDIS_PORT = 6379
REDIS_ITEMS_KEY = 'Newsspider:news'


ITEM_PIPELINES = {
   'Newsspider.pipelines.NewsspiderPipeline': 300,
    'scrapy_redis.pipelines.RedisPipeline': 400,
}

```

```python
#item.py
import scrapy


class NewsspiderItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    name = scrapy.Field()
    link = scrapy.Field()

```



### 将redis中的数据存到mysql数据库

```python
"""
把redis中的数据存到数据库中
"""

from redis import Redis
import settings
import pymysql
import json


class RedisPipeline(object):

    def __init__(self):
        """
        链接数据库
        """
        self.conn = pymysql.connect(
            host = '127.0.0.1',
            port = 3306,
            user = 'root',
            password = 'dai1993',
            db = 'news',
            charset = 'utf8'
        )
        self.cursor = self.conn.cursor()
        #链接redis
        self.rds = Redis('127.0.0.1',6379)

    def process_item(self):
        while True:
            data = self.rds.blpop(settings.REDIS_ITEMS_KEY)
            item = json.loads(data.decode('utf-8'))
            sql = "insert into news_spider(name,link) values (%s,%s)"
            self.cursor.execute(sql,(item['name'],item['link']))
            self.conn.commit()


    def close(self):
        self.cursor.close()
        self.conn.close()

redis_pipeline = RedisPipeline()
redis_pipeline.process_item()




```





### slave端的配置

1. 在爬虫项目中的setting.py文件中添加配置信息：

   ```python
   REDIS_URL = 'redis://redis_server ip:6379'  #maseter端的ip
   ```



### 运行分布式爬虫

```python
scrapy crawl myspider
```



```python
redis-cli -h masetr端ip
redis-cli > lpush newsspider:start_urls http://www.xxxxx.com/aaa/

```



redis中查看key对应的值：lrange Newsspider:news 0 -1

