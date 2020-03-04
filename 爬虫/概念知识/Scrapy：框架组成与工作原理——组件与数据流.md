## Scrapy：框架组成与工作原理——组件与数据流

### Scrapy组件与数据流

###### 1.Scrapy组件与数据流

```
ENGINE：引擎，是scrapy框架的核心；内部组件
SCHEDULER：调度器，负责对SPIDER提交的下载请求进行调度；内部组件
DOWNLOADER：下载器，负责下载页面，即发送HTTP请求和接受HTTP响应；内部组件
SPIDER：爬虫，负责从页面解析和提取数据，以及生成新的HTTP请求，用户组件
MIDDLEWARE：中间件，负责对HTTP请求和接受HTTP响应进行处理；可选组件
ITEM PIPELINE:数据管道，负责对爬取的数据进行处理，如去重、写入数据库等

对开发者来说，Spider是最核心的组件

```

###### 2.Scrapy框架数据流

```python
Scrapy数据流中涉及到三种对象：
Request：Scrapy框架中的HTTP请求对象。
Response：Scrapy框架中的HTTP响应框架。
Item：从页面中提取的数据对象

```

![](assets/20200212101836308_758874861.png =800x)


```python
#流程：
1.Spider将要爬取页面的URL构造Request对象，提交给Engine；图1

2.Request由Engine进入Scheduler，按照某种调度算法排队，之后某个时候从队列中出来，由Engine提交给Downloader；图2、3、4

3.Downloader根据Request中的URL地址发送一次HTTP请求到目标网站服务器，接受服务器返回的HTTP响应并构建一个Response对象（图5）并由Engine将Response提交给Spider（图6）

4.Spider提取Response中的数据，构造出item对象或者根据新的链接构造出Request对象，如果是Item对象，由Engine提交给Item pipeline，如果是新的Request，由Engine提交给Scheduler；（图7、8）

5.这个过程反复进行，直到爬完所有的数据，同时，数据对象在出入Spider和Downloader的时候可能会经过Middleware的进一步处理。



```

