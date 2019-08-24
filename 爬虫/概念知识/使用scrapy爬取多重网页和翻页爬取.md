## 使用scrapy爬取多重网页和翻页爬取

### 使用scrapy爬取多重页面

```python
#创建BtMovie项目
scrapy startproject BtMovie
#进入BtMovie目录后创建爬虫
scrapy genspider btdy www.btbtdy.net
```

###### items.py文件

```python
import scrapy


class BtmovieItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    title = scrapy.Field()
    content = scrapy.Field()
    magnet = scrapy.Field()

```



###### btdy.py文件

```python
import scrapy
from BtMovie.items import BtmovieItem

class BtdySpider(scrapy.Spider):
    name = 'bdty'
    allowed_domains = ['www.btbtdy.net']
    start_urls = ['http://www.btbtdy.net/']
    
    def parse(self, response):
        links = response.xpath('//div[@class="cts_ms"]/p/a/@href')
        for link in links:
            # print(link)
            yield response.follow(link,self.parse_content)
     
    def parse_content(self,response):
        print(response.xpath('//title'))
        movie = BtmovieItem()
        title = response.xpath('//h1/text()').extract()
        content = response.xpath('//div[@class="c05"]/span/a/@href')
        magnet = response.xpath('//[@id="nucms_downlist"]
                                /div[2]/ul/li/span/a/@href').extract()
        movie['title'] = title
        movie['content'] = content
        movie['magnet'] = magnet
        yield movie
```





### scrapy爬取分页及多重页面

```python
# -*- coding: utf-8 -*-
import scrapy
from BtMovie.items import BtmovieItem


class BtflSpider(scrapy.Spider):
    name = 'btfl'
    allowed_domains = ['www.btbtdy.net']
    # start_urls = ['http://www.btbtdy.net/btfl/dy1.html']


    def start_requests(self):
        urls = ['http://www.btbtdy.net/btfl/dy1.html']
        for url in urls:
            req = scrapy.Request(url,callback=self.parse)
            yield req



    def parse(self, response):
        titles = response.xpath('//div[@class="cts_ms"]/p/a/text()')



        #进一步解析详情页
        links = response.xpath('//div[@class="cts_ms"]/p/a/@href')
        for link in links:
            print(link)
            yield response.follow(link,self.parse_content)


            #判断是否有下一页，如果有下一页重复以上步骤
            next_page = response.xpath('//a[text()="下一页"]')
            print(next_page)
            if next_page:
                yield response.follow(next_page[0],callback=self.parse)





    def parse_content(self,response):
        print(response.xpath('//title'))
        movie = BtmovieItem()
        title = response.xpath('//h1/text()').extract()
        content = response.xpath('//div[@class="c05"]/span/text()').extract()
        magnet = response.xpath('//*[@id = nums_downlist]/div[2]/ul/li/span/a/@href')
        movie['title'] = title
        movie['content'] = content
        movie['magnet'] = magnet
        yield movie

```

