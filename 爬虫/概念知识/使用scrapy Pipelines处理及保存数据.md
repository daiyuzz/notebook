## 使用scrapy pipelines处理及保存数据

### 命令行传参

###### item.py

```python
import scrapy


class JobspiderItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    name = scrapy.Field()

```





```python
# -*- coding: utf-8 -*-
import scrapy
from Jobspider.items import JobspiderItem

class QcwySpider(scrapy.Spider):
    name = 'qcwy'
    allowed_domains = ['51job.com']
    # start_urls = ['http://51job.com/']



    def __init__(self,place=None,*args,**kwargs):
        super(QcwySpider, self).__init__(*args,**kwargs)
        if place is None:
            self.place = '杭州'
        else:
            self.place = place

        #与上面代码效果相同
        # self.place="杭州" if not place else place

    def start_requests(self):
        urls=['https://search.51job.com/list/{place_code},000000,0000,00,9,99,python,2,1.html'.format(place_code=self.get_place_code())]

        req = scrapy.Request(urls[0],callback=self.parse)
        yield req

    def get_place_code(self):
        place_map = {
            "杭州":"080200",
            "上海":"020000",
        }
        return place_map.get(self.place)





    def parse(self, response):
        print(response.xpath('//title/text()'))
		jobs = response.xpath('//*[@id="resultList"]/div[@class="el"]')
        print(jobs)
        for job in jobs:
            name = job.xpath('.//p/span/a/text()').extract_first().strip()
            item = JobspiderItem()
            item['name'] = name
            yield item

```



### 处理及保存数据

###### pipelines.py

```python
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://doc.scrapy.org/en/latest/topics/item-pipeline.html
import json
from scrapy.contrib.exporter import JsonItemExporter

class JobspiderPipeline(object):
    def process_item(self, item, spider):
        # print(item)
        return item


class JsonPipeline(object):

    def __init__(self):
        self.json_file = open('job.json','w')

    def process_item(self, item, spider):
        json_item = json.dumps(dict(item))
        self.json_file.write(json_item)
        self.json_file.write('\n')
        return item


    def close_spider(self,spider):
        print('close_spider is call')
        self.json_file.close()



class ScrapyJsonPipeline(object):
    def __init__(self):
        self.json_file = open('job_scrapy.json','wb')
        self.exporter = JsonItemExporter(self.json_file,encoding = 'utf-8')
        self.exporter.start_exporting()

    def process_item(self,item,spider):
        self.exporter.export_item(item)
        return item

    def close_spider(self,spider):
        self.exporter.finish_exporting()


class MySQLPipeline(object):
    """
    pymysql
    """
    def __init__(self):
        """
        连接数据库
        """
        self.conn = pymysql.connect(
            host='127.0.0.1',
            port = '3306',
            user = 'root',
            db = 'spider',
            password='dai1993',
            charset = 'utf8'
        )
        self.cursor = self.conn.cursor()

        

    def process_item(self,item,spider):
        """
        把每条数据插入数据库

        """
        sql = "insert into spider.qcwy(name) values(%s)"
        self.cursor.execute(sql,(item['name'],))
        self.conn.commit() #提交数据
        return item

    def close_spider(self,spider):
        """
        关闭数据链接

        """
        self.cursor.close()
        self.conn.close()

        
#将数据保存到Mongodb中
class MongoDBPipeline(object):
    """
    pymongo
    """
    def __init__(self):
        """
        创建一个客户端
        """
        self.client = pymongo.MongoClient(host='127.0.0.1',port=27017)
        self.db = self.client['spider']

        self.coll = self.db['qcwy_collection']

    def process_item(self,item,spider):
        dict_item = dict(item)
        self.coll.insert(dict_item)
        return item

    def close_spider(self,spider):
        self.client.close()




```



```python
#setting.py
ITEM_PIPELINES = {
   'Jobspider.pipelines.JobspiderPipeline': 300,
   'Jobspider.pipelines.JsonPipeline':301,
   'Jobspider.pipelines.ScrapyJsonPipeline':302,
    'Jobspider.pipelines.MySQLPipeline':303,
   'Jobspider.pipelines.MongoDBPipeline':304,
}

```



###### #注：在mysql数据库中表和字段需要手动创建，而Mongodb则自动创建；