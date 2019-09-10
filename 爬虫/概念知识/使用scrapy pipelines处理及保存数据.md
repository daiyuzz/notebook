## 使用scrapy pipelines处理及保存数据

### mysql及mongo存贮

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



### 时间处理

例如：微博爬虫中处理微博发表时间

```python
class TimePipeline():                                              
    def process_item(self,item,spider):                            
        if isinstance(item,UserItem) or isinstance(item,WeiboItem):
            now = time.strftime('%Y-%m-%d %H:%M',time.localtime()) 
            item['crawled_at'] = now                               
        return item                                                
                                                                   
```



```python
class WeiboscrapyPipeline(object):
    def parse_time(self, date):
        """格式化时间"""
        if re.match('刚刚', date):
            date = time.strftime('%Y-%m-%d %H:%M', time.localtime(time.time()))
        if re.match('\d+分钟前', date):
            minute = re.match('(\d+)', date).group(1)
            date = time.strftime('%Y-%m-%d %H:%M', time.localtime(time.time() - float(minute) * 60))
        if re.match('\d+小时前', date):
            hour = re.match('(\d+)', date).group(1)
            date = time.strftime('%Y-%m-%d %H:%M', time.localtime(time.time() - float(hour) * 60 * 60))
        if re.match('昨天.*', date):
            date = re.match('昨天(.*)', date).group(1).strip()
            date = time.strftime('%Y-%m-%d', time.localtime() - 24 * 60 * 60) + ' ' + date
        if re.match('\d{2}-\d{2}', date):
            date = time.strftime('%Y-', time.localtime()) + date + '00:00'
        return date

    def process_item(self, item, spider):
        if isinstance(item, WeiboItem):
            if item.get('created_at'):
                item['created_at'] = item['created_at'].strip()
                item['created_at'] = self.parse_time(item.get('created_at'))
            if item.get('pictures'):
                item['pictures'] = [pic.get('url') for pic in item.get('pictures')]
            return item

```

