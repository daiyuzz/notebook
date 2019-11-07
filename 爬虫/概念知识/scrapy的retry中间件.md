# scrapy的retry中间件

不管你的主机配置多么好，网速多么给力，在scrapy的大规模的任务中，最终爬取的item数量都不会等于期望爬取的数量，也就是说总有那么一些漏网之鱼，通过分析scrapy日志，可以知道造成失败的原因有一下两种情况：
1、exception_count
2、httperror

![](assets/20191016180305115_400331058.png =800x)

以上的不管是exception还是httperror，scrap中都有对应的retry机制，在`settings.py`文件中我们可以设置有关重试的参数，等运行遇到异常或错误的时候，scrapy就会自动处理这些问题，其中最关键的部分就是重试中间件，下面让我们看一下scrapy的retry middleware.

## RetryMiddle源码分析
在scrapy项目中的`middlewares.py`文件中 敲如下代码：
```python
from scrapy.downloadermiddlewares.retry import Retrymiddleware
```
进入RetryMiddleware文件所在的位置，源码如下：
```python
class RetryMiddleware(object):

    # IOError is raised by the HttpCompression middleware when trying to
    # decompress an empty response
    # 需要重试的异常状态，可以看出，其中有些是上面log中的异常
    EXCEPTIONS_TO_RETRY = (defer.TimeoutError, TimeoutError, DNSLookupError,
                           ConnectionRefusedError, ConnectionDone, ConnectError,
                           ConnectionLost, TCPTimedOutError, ResponseFailed,
                           IOError, TunnelError)

    def __init__(self, settings):
      # 读取 settings.py 中关于重试的配置信息，如果没有配置重试的话，直接跳过
        if not settings.getbool('RETRY_ENABLED'):
            raise NotConfigured
        self.max_retry_times = settings.getint('RETRY_TIMES')
        self.retry_http_codes = set(int(x) for x in settings.getlist('RETRY_HTTP_CODES'))
        self.priority_adjust = settings.getint('RETRY_PRIORITY_ADJUST')

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler.settings)
# 如果response的状态码，是我们要重试的
    def process_response(self, request, response, spider):
        if request.meta.get('dont_retry', False):
            return response
        if response.status in self.retry_http_codes:
            reason = response_status_message(response.status)
            return self._retry(request, reason, spider) or response
        return response
# 出现了需要重试的异常状态，
    def process_exception(self, request, exception, spider):
        if isinstance(exception, self.EXCEPTIONS_TO_RETRY) \
                and not request.meta.get('dont_retry', False):
            return self._retry(request, exception, spider)
# 重试操作
    def _retry(self, request, reason, spider):
        retries = request.meta.get('retry_times', 0) + 1

        retry_times = self.max_retry_times

        if 'max_retry_times' in request.meta:
            retry_times = request.meta['max_retry_times']

        stats = spider.crawler.stats
        if retries <= retry_times:
            logger.debug("Retrying %(request)s (failed %(retries)d times): %(reason)s",
                         {'request': request, 'retries': retries, 'reason': reason},
                         extra={'spider': spider})
            retryreq = request.copy()
            retryreq.meta['retry_times'] = retries
            retryreq.dont_filter = True
            retryreq.priority = request.priority + self.priority_adjust

            if isinstance(reason, Exception):
                reason = global_object_name(reason.__class__)

            stats.inc_value('retry/count')
            stats.inc_value('retry/reason_count/%s' % reason)
            return retryreq
        else:
            stats.inc_value('retry/max_reached')
            logger.debug("Gave up retrying %(request)s (failed %(retries)d times): %(reason)s",
                         {'request': request, 'retries': retries, 'reason': reason},
                         extra={'spider': spider})


```
查看源码我们发现，对于http code和response，该中间件会通过process_response方法来处理，处理办法比较简单，判断response.status是否在`retry_http_code`集合中，这个集合是读取的配置文件：

```python
RETRY_ENABLED = True # 默认开启失败重试，一般关闭
RETRY_TIMES = 3    # 失败重试次数，默认两次
RETRY_HTTP_CODES = [500,502,503,504,522,524,408]    # 碰到这些状态码，才开启重试
```

对于`httperror`的处理也是同样的道理，定义了一个`EXCEPTIONS_TO_RETRY`的列表，里面存放所有的异常类型，然后判断传入的异常是否存在于该集合中，如果在就进入retry逻辑，不在就忽略。


## 源码思想的应用
了解scrapy如何处理异常之后，就可以利用这些思想，写一个`middleware`，对爬取失败的漏网之鱼进行捕获，以方便以后补爬。

1、在`middleware.py`中`from scrapy.downloadmiddlewares.retry import RetryMiddleware`,写一个class，继承自`RetryMiddleware`
2、对父类的`process_response（）`和`process_exception()`方法进行重写；
3、将该`middleware`加入`settings.py`
4、注意事项：该中间件的`Order_code`不能过大，如果过大就会越接近下载器，就会优先于`RetryMiddleware`处理`response`，但这个中间件是用来处理最终的错误的，即当一个response 500 进入中间链路时，需要先经过retry中间件处理，不能先由我们写的中间件来处理，它不具有retry的功能，接收到500的response就直接放弃掉该request直接return了，这是不合理的。 只有经过retry之后仍然有异常的requests才应该有我们写的中间件来处理，比如再次的retry、return一个重新构造的response，但是如果你为了加快爬虫速度，不设置retry也可以。



```python
class GetFailedUrl(RetryMiddleware):
    def __init__(self,settings):
        self.max_retry_time = settings.getint('RETRY_TIMES')
        self.retry_http_codes = set(int(x) for x in settings.getlist('RETRY_HTTP_CODES'))
        self.priority_adjust = settings.getint('RETRY_PRIORITY_ADJUST')

    def process_response(self,request,response,spider):
        if response.status in self.retry_http_codes:
            with open(str('spider.name')+".text","a")as f:
                f.write(response.url+'\n')
            return response
         return response
        
    def process_exception(self,request,exception,spider):
        # 出现异常的处理
        if isinstance(exception,self.EXCEPTIONS_TO_RETRY):
            wirh open(str(spider.name)+".txt","a") as f:
                f.write(str(request)+"\n")
            return None
```

在`setting.py`中添加该中间件：

```python
   'myspider.middlewares.TabelogDownloaderMiddleware': 543,
    'myspider.middlewares.RandomProxy': 200,
    'myspider.middlewares.GetFailedUrl': 220,
```

