# 9.1 scrapy-splasht动态爬虫

## 安装Docker

> Docker是一个开源的软件部署解决方案
>
> 官方文档：https://docs.docker.com
>
> 中文参考文档：http://www.docker.org.cn

### 在Ubuntu中安装二进制Docker (Install Docker CE from binaries)

优点是安装简单，缺点是无服务、无配置文件，相当于Windows平台中的绿色软件。

### 使用安装脚本自动安装Docker及其依赖

wget -qO- <https://get.docker.com/> | sh

sudo service docker start

docker run hello-world

## 安装scrapy-splash

`pip install scrapy-splash`

## 配置scrapy-splash

```python
# 渲染服务的url
SPLASH_URL = 'http://127.0.0.1:8050'

#下载器中间件
DOWNLOADER_MIDDLEWARES = {
    'scrapy_splash.SplashCookiesMiddleware': 800,
    'scrapy_splash.SplashMiddleware': 801,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 802,
}
# 去重过滤器
DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter'
# 使用Splash的Http缓存
HTTPCACHE_STORAGE = 'scrapy_splash.SplashAwareFSCacheStorage'
```
## 编写spider
```python
class SplashSpider(scrapy.Spider):
    name = 'scrapy_splash'
    start_urls = [
        'https://movie.douban.com/subject/26752088/'
    ]

    #request需要封装成SplashRequest
    def start_requests(self):
        urls = ['https://movie.douban.com/subject/26752088/']
        for url in urls:

            yield SplashRequest(url, callback=self.parse)

    def parse(self, response):
        item = SplashTestItem()
        print("~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`")
        #//*[@id="hot-comments"]/div[@class="comment-item"]'
        comments = response.xpath('//*[@id="hot-comments"]/div'
                                  '[@class="comment-item"]')
        for comment in comments:
            #.//span[@class="votes"]/text()
            item['votes'] = comment.xpath('.//span[@class="votes"]/text()'
                                          '').extract_first()
            # .//span[@class="short"]/text()
            item['short'] = comment.xpath('.//span[@class="short"]/text()').extract_first()
            yield item

```
## 9.2 CrawlSpider

```python
from scrapy.spiders import CrawlSpider, Rule
from scrapy.linkextractors import LinkExtractor


class DushuSpider(CrawlSpider):
    name = 'dushu'
    allowed_domains = ['www.dushu.com']
    start_urls = ['http://www.dushu.com/']
    rules = (
        Rule(LinkExtractor(allow=r'/guoxue/\d+'), follow=True),
        Rule(LinkExtractor(allow=r'/guoxue/\d+'), callback='parse_item'),
    )

    def parse_item(self, response):
        print(response.xpath('//title/text()'))
```

## 9.3 运行爬虫

### 使用runspider命令运行

``scrapy runspider  some_spider.py``

### 使用crawl命令运行

``scrapy crawl  spider_name``

### 使用cmdline运行

```
from scrapy import cmdline

cmdline.execute("scrapy crawl movie".split())
```

### 使用CrawlerProcess运行

```
import scrapy
from scrapy.crawler import CrawlerProcess
from .spiders import spider_a
from .spiders import spider_b
process = CrawlerProcess()

process.crawl(spider_a)
process.crawl(spider_b)
process.start()
```
## 9.4 暂停，恢复爬虫

```
scrapy crawl somespider -s JOBDIR=crawls/somespider-1
```

## 9.5 使用中间件实现IP代理池

```python
class MyProxyMidleware(object):
     
    def process_request(self, request, spider):
        request.meta['proxy']  = random.choice(my_proxies.PROXY)
```



## 9.6 使用中间件实现User-Agent代理池

```python
from . import settings

class RandomUAMiddleware(object):
    def process_request(self, request, spider):
        ua = random.choice(settings.USER_AGENT_LIST)
        print(request.headers)
        if ua:
            request.headers.setdefault('User-Agent', ua)
        print(request.headers)
```

```python
#使用插件实习User-Agent代理
pip install fake_useragent


#setting.py

USER_AGENT = 'Dushu (+http://www.yourdomain.com)'

DOWNLOADER_MIDDLEWARES = {
   'Dushu.middlewares.DushuDownloaderMiddleware': 543,
   # 'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware':None,
   'Dushu.middlewares.DushuUserAgentMiddlerware':540
}



```





## 9.7 使用Scrapy登录网站

```
class LoginSpider(scrapy.Spider):
    name = 'login'
    allowed_domains = ['127.0.0.1']
 
    def start_requests(self):
        start_urls = ['http://127.0.0.1:8888/login/']
        for url in start_urls:
            request = scrapy.Request(url, callback=self.login)
            yield request
 
    def login(self, response):
        request = FormRequest.from_response(response, formdata={
            'username': 'admin', 'password': 'qianfeng'},
            callback=self.after_login
 
        )
        yield request
 
    def after_login(self, response):
        print(response.text)
```



