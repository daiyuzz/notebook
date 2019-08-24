## 使用selenium爬取动态网页



## 使用Selenium爬取动态网页

使用selenium优点：所见既所得

通过page_source属性可以获得网页源代码

selenium可以驱动浏览器完成各种操作，如填充表单、模拟点击等。



获取单个节点的方法：

```python
find_element_by_id
find_element_by_name
find_element_by_xpath
find_element_by_link_text
find_element_by_partical_link_text
find_element_by_tag_name
find_element_by_class_name
find_element_by_css_selector

#另外，selenium还提供了通用方法find_element(),它需要传入两个参数：查找方式By和值
```



获取多个节点的方法：

```python
find_elements_by_id
find_elements_by_name
find_elements_by_xpath
find_elements_by_link_text
find_elements_by_partical_link_text
find_elements_by_tag_name
find_elements_by_class_name
find_elements_by_css_selector
```



节点交互：

```python
比较常用的方法：
输入文字时使用send_keys()方法，清空文字使用clear（）方法，点击按钮使用click（）方法
```



执行javascript

```python
对于某些操作，Selenium API并没有提供，比如，下拉进度条，可以直接模拟运行JavaScript，此时使用execute_script（）方法即可实现

```

```python
from selenium import webdriver

browser = webdriver.Chorme()
browser.get('https://www.zhihu.com/explore')
browser.execute_script('window.scrollTo(0,document.body.scrollHeight)')
browser.execute_script('alert("To Bottom")')
```

有了这个方法，基本上API没有提供的所有功能都可以执行JavaScript的方式来实现





异常处理（使用try except语句来捕获各种异常）

```python
from selenium import webdriver
from selenium.common.exceptions import TimeoutException,NoSuchElementException

browser = webdriver.Chrome()
try:
    browser.get('https://www.baidu.com')
except TimeoutException:
    print("Time Out")
try:
    browser.find_element_by_id('hello')
except NoSuchElementException:
    print('No Element')
finally:
    browser.close()
```










```python
scrapy startproject Taobao
scrapy genspider taobao www.taobao.com
```

```python
#taobao.py

import scrapy

class TaobaoSpider(scrapy.Spider):
    name = 'taobao'
    allowed_domains = ['www.taobao.com']
    
    def start_requests(self):
        urls = ['http://www.taobao.com/']
       	for url in urls:
            req = scrapy.Request(url,callback='self.parse，meta={"use_selenium":True}')
            yield req
            
    def parse(self,response):
        print(response.body)
       

```

```python
#setting.py
#启用
ROBOTSTXT_OBEY = False

DOWNLOADER_MIDDLEWARES = {
   'Taobao.middlewares.TaobaoDownloaderMiddleware': 543,
}

ITEM_PIPELINES = {
   'Taobao.pipelines.TaobaoPipeline': 300,
}


```



```python
#middlewares.py
#在中间件中设置selenium访问动态网页
#在``def process_request(self,request,spider)``中添加下列代码

def process_request(self,request,spider):
    #使用两种方法判断是否使用selenium
    #if spider.name == "taobao":    #第一种方法
    if request.meta.get('use_selenium'):
        #设置无界面运行ChromeDriver
        option = Options（）
        option.add_argument('--headless')
        driver = webdriver.Chrome(chrome_options=option)
        
        #有界面运行ChromeDriver,如使用有界面时，将下面一行代码替换上面三行代码
        #driver = webdriver.Chorme()
        
        driver.implicitly_wait(15)
        driver.get(request.url)
        
        #执行js，获取动态页面的整个页面
        js = 'window.scrollTo(0,document.body.scrollHeight)'
        driver.execute_script(js)
        content = driver.page_source
        
        resp = HtmlResponse(request.url,request=request,body=content)
        return resp  #被解析函数parse接收
    return None
        

```


