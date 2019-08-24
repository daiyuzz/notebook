## item Pipeline

```python
当item在Spider中被收集之后，它将会被传递到Item Pipeline，一些组件会按照一定的顺序执行对item的处理
```

```python
以下是item pipeline的一些典型应用：

清理HTML数据
验证爬取的数据（检查item包含某些字段）
查重（并丢弃）
将爬取结果保存到数据库中
```



```python
每个item pipeline组件是一个独立的python类，同时必须实现以下方法：

process_item(item,spider)

每个item pipeline组件都需要调用该方法，这个方法必须返回一个item（或任何继承类）对象，或是抛出DropItem异常，被丢弃的item将不会被之后的pipline组件所处理

参数：item（item对象）-被爬取的item
	spider（Spider对象）-爬取该item的spider
    
    
```

### Item pipeline样例

#### 验证价格，同时丢弃没有价格的item

```python
from scrapy.exceptions import DropItem

class PricePipeline(object):

    vat_factor = 1.15

    def process_item(self, item, spider):
        if item['price']:
            if item['price_excludes_vat']:
                item['price'] = item['price'] * self.vat_factor
            return item
        else:
            raise DropItem("Missing price in %s" % item)
```



#### 将item写入JSON文件

以下pipeline将所有爬取到的item，存储到一个独立的item.jl文件，每行包含一个序列化为JSON格式的item

```python
import json

class JsonWritePipeline(object):
    def __init__(self):
        self.file = open('item.jl','wb')
        
    def process_item(self,item,spider):
        line = json.dumps(dict(item))+"/n"
        self.file.write(line)
        return item
```



#### 去重

```python
一个用于去重的过滤器，丢弃那些已经被处理过的item。让我们假设我们的item有一个唯一的id，但我们spider返回的多个item中包含相同的id

```



```python
from scrapy.exceptions import DropItem

class DuplicatesPipeline(object):
    def __init__(self):
        self.ids_seen = set()
        
    def process_item(self,item,spider):
        if item['id'] in self.ids_seen:
            raise DropItem("Duplicate item found:%s"%item)
        else:
            self.ids_seen.add(item['id'])
            return item
```



### 启动一个Item Pipeline组件

```python
ITEM_PIPELINES = {
    'myproject.pipelines.PricePipeline': 300,
    'myproject.pipelines.JsonWriterPipeline': 800,
}
```

