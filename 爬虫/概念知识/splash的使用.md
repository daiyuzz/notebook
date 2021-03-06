# splash的使用

Splash是一个JavaScript渲染服务，是一个HTTP API的轻量级浏览器，同时它对接了Python的的Twisted和QT库。利用它，我们同样可以实现动态渲染页面的抓取。

## 1、功能介绍
利用Splash我们可以实现如下功能：
- 异步方式处理多个网页渲染过程
- 获取渲染后的页面的源代码或截图
- 通过关闭图片渲染或者使用 Adblock规则来加快页面渲染速度
- 可执行特定的 JavaScript 脚本；
- 可通过Lua脚本来控制页面渲染过程
- 获取渲染的详细过程并通过 HAR(HTTP Archive)格式呈现

使用`sudo docker run -p 8050:8050 scrapinghub/splash`开启splash服务

## 2、scrapy-splash的使用

scrapy-splash是scrapy的一个插件，用来爬取动态网页，其效果和scrapy selenium phantomjs是一样的，都是通过渲染js得到动态网页然后实现网页解析，Splash是官推的js渲染引擎，和scrapy结合的比较好，使用的是webkit开发的轻量级无界面的浏览器，渲染之后结果和静态爬取一样可以直接解析处理，只是splash实在docker中运行。

与scrapy selenium相比，scrapy通过Downloader Middleware的方式实现了与selenium的对接，但是这种方式是阻塞的，也就是说这样就破坏了scrapy异步处理的逻辑，速度或收到影响，而scrapy-splash不会被破坏其异步加速逻辑。

Scrapy-Splash的安装分为两部分：
一个是Splash服务的安装，具体是通过Docker安装，会启动一个Splash服务，我们可以通过它的接口来实现JavaScript页面的加载。

另一个是Scrapy-Splash的python库安装，安装之后即可在Scrapy中使用Splash服务。



execute端点：
被用来提供如下服务：当用户想在页面中执行自己定义的js代码，如：用js代码模拟浏览器进行页面操作
这里，我们将execute看成是一个可以模拟用户行为的浏览器，而用户的行为我们用lua脚本进行定义

Splash对象常用的属性和方法总结
- splash：args属性--传入用户参数的表，通过该属性可以访问用户传入的参数，如splash.args.url、 splash.args.wait
- splash.images_enable属性--用于开启/禁止图片加载，默认值为True
- splash.go方法--请求url页面
- splash：wait方法--等待渲染秒数
- splash:evaljs方法--在当前页面下，执行一段js代码，并返回最后一句表达式的值
- splash:runjs方法--在当前页面下，执行一段js代码
- splash:url方法--获取当前页面的url
- splash：html--获取当前页面的HTML文章
- splash：get__cookies--获取cookies信息

** 在scrapy_splash中定义了一个SplashRequest类，用户只需要使用scrapy_splash.SplashRequest来替代scrapy.Request发送请求。**
该构造器常用的参数如下：
- url--待爬取的url地址
- headers--请求头
- cookies--cookies信息
- args--传递给splash的参数，如wait\timeout\images\js_source等
- cache_args--针对参数重复调用或数据量太大的情况，让Splash缓存该参数
- endpoint--Splash服务端点
- splash_url--Splash服务器地址，默认为None

### settings.py
- Splash服务器地址：

```python
SPLASH_URL='http://127.0.0.1:8050'
```
- 设置去重过滤器：
```python
DUPEFILTER_CLASS='scrapy_splash.SplashAwareDupeFilter'
```
- 开启Splash中间件，注意启动的顺序：
```python
DOWNLOADER_MIDDLEWARES = {
    'scrapy_splash.SplashCookiesMiddleware': 723,
    'scrapy_splash.SplashMiddleware': 725,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810,
}
```
- Enable SplashDeduplicateArgsMiddleware:
```python
SPIDER_MIDDLEWARES = {
    'scrapy_splash.SplashDeduplicateArgsMiddleware':100,
}
```

- 如果使用scrapy http缓存系统，需要开启这个scrapy-splash的缓存系统：
```python
HTTPCACHE_STORAGE = 'scrapy_splash.SplashAwareFSCacheStorage'
```


## 爬虫实例
以爬去京东上`ipad`商品为例，展示scrapy-splash在scrapy中如何进行工作的。

商品列表页面采用`ajax`加载，显示30个商品，将页面拉倒底部显示另外30个商品，所以需要splash执行拖动web页面的操作。
```python
lua_script = """
    function main(splash)                     
        splash:go(splash.args.url)        --打开页面
        splash:wait(2)                    --等待加载
        splash:runjs("document.getElementsByClassName('page clearfix')[0].scrollIntoView(true)") --运行js代码
        splash:wait(2)                    --等待加载
        return splash:html()              --返回页面数据
    end
"""
```
其中`splash:runjs()`中的内容就是将页面滑动到底部的`WEB API`，最终将网页内容返回。

![](assets/20191006094519662_284534218.png)
整个项目树，如上图所示；
### jd.py
```python


# -*- coding: utf-8 -*-
import scrapy
from scrapy_splash import SplashRequest
from urllib.parse import quote
from jd_splash.items import JdSplashItem

lua_script = """
    function main(splash)
        splash:go(splash.args.url)        --打开页面
        splash:wait(2)                    --等待加载
        splash:runjs("document.getElementsByClassName('page clearfix')[0].scrollIntoView(true)") --运行js代码
        splash:wait(2)                    --等待加载
        return splash:html()              --返回页面数据
    end
"""


class JdSpider(scrapy.Spider):
    name = 'jd'
    allowed_domains = ['www.jd.com']
    base_url = 'https://search.jd.com/Search?keyword='

    def start_requests(self):
        for keyword in self.settings.get('KEYWORDS'):
            for i in range(1, self.settings.get('MAX_PAGE') + 1):
                url = self.base_url + quote(keyword) + '&enc=utf-8&page={}'.format(2 * i + 1)
                yield SplashRequest(
                    url,
                    endpoint='execute',
                    args={'lua_source': lua_script},
                    cache_args=['lua_source'],
                    callback=self.parse,
                    dont_filter=True,
                )

    def parse(self, response):
        for items in response.xpath('//ul[@class="gl-warp clearfix"]/li[@class="gl-item"]'):
            item = JdSplashItem()
            item['price'] = items.xpath('.//div[@class="p-price"]//i/text()').extract_first()
            item['title'] = items.xpath(
                'normalize-space(.//div[contains(@class,"p-name p-name-type-2")])').extract_first()
            item['shop'] = ''.join(items.xpath('.//div[contains(@class,"p-shop")]//a//text()').extract()).strip()
            item['commit'] = ''.join(items.xpath('.//div[contains(@class,"p-commit")]//text()').extract()).strip()
            item['href'] = 'https://' + \
                           (''.join(items.xpath('.//div[contains(@class,"p-img")]/a/@href').extract()).strip()).split(
                               '//', 1)[1]
            yield item

```

### items.py
```python
import scrapy


class JdSplashItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    price = scrapy.Field()
    title = scrapy.Field()
    shop = scrapy.Field()
    commit = scrapy.Field()
    href = scrapy.Field()
```

### middleware.py
```python
# 更换ua的中间件
from fake_useragent import UserAgent


class RandomUserAgentMiddleware(object):
    def __init__(self, crawler):
        super(RandomUserAgentMiddleware, self).__init__()
        self.ua = UserAgent()
        self.ua_type = crawler.settings.get('RANDOM_UA_TYPE', 'random')

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler)

    def process_request(self, request, spider):
        def get_ua():
            return getattr(self.ua, self.ua_type)

        if get_ua():
            request.headers.setdefault('User-Agent', get_ua())

```

### settings.py
```python
# -*- coding: utf-8 -*-

BOT_NAME = 'jd_splash'

SPIDER_MODULES = ['jd_splash.spiders']
NEWSPIDER_MODULE = 'jd_splash.spiders'


USER_AGENT = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36'
# Obey robots.txt rules
ROBOTSTXT_OBEY = False


SPIDER_MIDDLEWARES = {
    # 'jd_splash.middlewares.JdSplashSpiderMiddleware': 543,
    'scrapy_splash.SplashDeduplicateArgsMiddleware': 100,
}

# Enable or disable downloader middlewares
# See https://docs.scrapy.org/en/latest/topics/downloader-middleware.html
DOWNLOADER_MIDDLEWARES = {
    'jd_splash.middlewares.RandomUserAgentMiddleware': 543,
    'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware': None,
    'scrapy_splash.SplashCookiesMiddleware': 723,
    'scrapy_splash.SplashMiddleware': 725,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810,
}

# splash服务器地址
SPLASH_URL = 'http://127.0.0.1:8050'

# 设置去重过滤器
DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter'

RANDOM_UA_TYPE = 'random'

KEYWORDS = ['ipad']

MAX_PAGE = 20

FEED_EXPORT_ENCODING = 'utf-8'
```

- 注意：由于中间件中设置了随机ua，在settings.py中`DOWNLOADER_MIDDLEWARES`中需要使其生效。
```python
'jd_splash.middlewares.RandomUserAgentMiddleware': 543,
'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware': None,
```

### run.py
```python
from scrapy import cmdline

cmdline.execute("scrapy crawl jd -o ipad.json".split())
```
使用命令行，将结果存入json文件中，在settings.py中需设置`FEED_EXPORT_ENCODING = 'utf-8'`


