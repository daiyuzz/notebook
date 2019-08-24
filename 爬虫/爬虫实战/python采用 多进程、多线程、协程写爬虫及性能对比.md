### python 多进程/多线程/协程写爬虫及性能对比



####  一、区别

首先我们来了解下python中的进程，线程以及协程！

从操作系统的角度：

进程和线程，都是一种CPU的执行单元。

进程：表示一个程序的上下文执行活动（打开、执行、保存...）

线程：进程执行程序时候的最小调度单位（执行a，执行b...)

一个程序至少有一个进程，一个进程至少有一个线程。

 

并行 和 并发：


并行：多个CPU核心，不同的程序就分配给不同的CPU来运行。可以让多个程序同时执行。

cpu1 -------------
cpu2 -------------
cpu3 -------------
cpu4 -------------

并发：单个CPU核心，在一个时间切片里一次只能运行一个程序，如果需要运行多个程序，则串行执行。

cpu1　　----　　----

cpu1 　　　----　　----



多进程：密集CPU任务，需要充分使用多核CPU资源（服务器，大量的并行计算）的时候，用多进程。 multiprocessing
缺陷：多个进程之间通信成本高，切换开销大。

多线程：密集I/O任务（网络I/O，磁盘I/O，数据库I/O）使用多线程合适。
threading.Thread、multiprocessing.dummy
缺陷：同一个时间切片只能运行一个线程，不能做到高并行，但是可以做到高并发。

协程：又称微线程，在单线程上执行多个任务，用函数切换，开销极小。不通过操作系统调度，没有进程、线程的切换开销。genvent，monkey.patchall

<u>多线程请求返回是无序的，那个线程有数据返回就处理那个线程，而协程返回的数据是有序的。</u>

缺陷：单线程执行，处理密集CPU和本地磁盘IO的时候，性能较低。处理网络I/O性能还是比较高.



### 二、代码演示

下面以豆瓣电影为例，采用三种爬取方式



#### 1.多进程（Process多进程）耗时1.18s

```python
# -*- coding=utf-8 -*-

from multiprocessing import Process,Queue

import time
from lxml import etree
import requests

class DoubanSpider(Process):
    def __init__(self,url,q):
        # 重写父类的__init__方法
        super(DoubanSpider, self).__init__()
        self.url = url
        self.q = q
        self.headers = {
            'Host': 'movie.douban.com',
            'Referer': 'https://movie.douban.com/top250?start=225&filter=',
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.104 Safari/537.36',
        }

    def run(self):
        self.parse_page()

    def send_request(self,url):
        i = 0
        while i <= 3:
            try:
                print("[INFO]请求url"+url)
                return requests.get(url=url,headers=self.headers).content
            except Exception as e:
                print("[INFO] %s%s"%(e,url))
                i += 1


    def parse_page(self):
        response = self.send_request(self.url)
        html = etree.HTML(response)
        node_list = html.xpath("//div[@class='info']")
        for moive in node_list:
            title = moive.xpath('.//a/span/text()')[0]
            score = moive.xpath('.//div[@class="bd"]//span[@class="rating_num"]/text()')[0]

            # 将每一部电影的名称跟评分加入到队列
            self.q.put(score + "\t" + title)


def main():
    # 创建一个队列用来保存进程获取到的数据
    q = Queue()
    base_url = 'https://movie.douban.com/top250?start='
    url_list = [base_url+str(num) for num in range(0,225+1,25)]

    # 保存进程
    Process_list = []
    # 创建并启动进程
    for url in url_list:
        p = DoubanSpider(url,q)
        p.start()
        Process_list.append(p)

    # 让主进程等待子进程执行完
    for i in Process_list:
        i.join()

    while not q.empty():
        print(q.get())

if __name__ == "__main__":
    start = time.time()
    main()
    print("[INFO]耗时：%s"%(time.time()-start))

        
```



![1551176074300](C:\Users\带鱼\AppData\Roaming\Typora\typora-user-images\1551176074300.png)



#### 2.多线程爬虫（Thread），耗时0.35s

 ```python
# -*- coding=utf-8 -*-

from threading import Thread
from queue import Queue
import time
from lxml import etree
import requests


class DoubanSpider(Thread):
    def __init__(self, url, q):
        # 重写父类的__init__方法
        super(DoubanSpider, self).__init__()
        self.url = url
        self.q = q
        self.headers = {
            'Host': 'movie.douban.com',
            'Referer': 'https://movie.douban.com/top250?start=225&filter=',
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.104 Safari/537.36',
        }

    def run(self):
        self.parse_page()

    def send_request(self, url):
        i = 0
        while i <= 3:
            try:
                print("[INFO]请求url" + url)
                html = requests.get(url=url, headers=self.headers).content
            except Exception as e:
                print("[INFO] %s%s" % (e, url))
                i += 1
            else:
                return html

    def parse_page(self):
        response = self.send_request(self.url)
        html = etree.HTML(response)
        node_list = html.xpath("//div[@class='info']")
        for moive in node_list:
            title = moive.xpath('.//a/span/text()')[0]
            score = moive.xpath('.//div[@class="bd"]//span[@class="rating_num"]/text()')[0]

            # 将每一部电影的名称跟评分加入到队列
            self.q.put(score + "\t" + title)


def main():
    q = Queue()
    base_url = 'https://movie.douban.com/top250?start='
    url_list = [base_url + str(num) for num in range(0, 255 + 1, 25)]

    # 保存线程
    Thread_list = []
    # 创建并启动线程
    for url in url_list:
        p = DoubanSpider(url, q)
        p.start()
        Thread_list.append(p)

    # 让主线程等待子线程执行完成
    for i in Thread_list:
        i.join()

    while not q.empty():
        print(q.get())


if __name__ == '__main__':
    start = time.time()
    main()
    print("[INFO]耗时：%s" % (time.time() - start))

 ```

![1551178356126](C:\Users\带鱼\AppData\Roaming\Typora\typora-user-images\1551178356126.png)



#### 3.采用协程爬取，耗时XX，（gevent需在linux/uinux运行，windows下运行不稳定）

```python
# -*- coding=utf-8 -*-
from queue import Queue
import time
from lxml import etree
import requests
import gevent

# 打上猴子补丁
from gevent import monkey

monkey.patch_all()


class DoubanSpider(object):
    def __init__(self):
        # 创建一个队列用来保存进程获取到的数据
        self.q = Queue()
        self.headers = {
            'Host': 'movie.douban.com',
            'Referer': 'https://movie.douban.com/top250?start=225&filter=',
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.104 Safari/537.36',
        }

    def run(self, url):
        self.parse_page(url)

    def send_request(self, url):
        i = 0
        while i <= 3:
            try:
                print("[INFO]请求url" + url)
                html = requests.get(url=url, headers=self.headers).content
            except Exception as e:
                print("[INFO] %s%s" % (e, url))
                i += 1
            else:
                return html

    def parse_page(self, url):
        response = self.send_request(url)
        html = etree.HTML(response)
        node_list = html.xpath("//div[@class='info']")
        for moive in node_list:
            title = moive.xpath('.//a/span/text()')[0]
            score = moive.xpath('.//div[@class="bd"]//span[@class="rating_num"]/text()')[0]

            # 将每一部电影的名称跟评分加入到队列
            self.q.put(score + "\t" + title)

    def main(self):
        base_url = 'https://movie.douban.com/top250?start='
        url_list = [base_url + str(num) for num in range(0, 255 + 1, 25)]
        # 创建协程并执行
        job_list = [gevent.spawn(self.run, url) for url in url_list]
        # 让线程等待所有的任务完后，再继续执行。
        gevent.joinall(job_list)

        while not self.q.empty():
            print(self.q.get())


if __name__ == '__main__':
    start = time.time()
    douban = DoubanSpider()
    douban.main()
    print("[INFO]耗时%s" % (time.time() - start))

```



![1551178649168](C:\Users\带鱼\AppData\Roaming\Typora\typora-user-images\1551178649168.png)



#### 4.单线程爬取，耗时3.02s

```python
# -*- coding=utf-8 -*-

from multiprocessing import Process, Queue

import time
from lxml import etree
import requests




headers = {
    'Host': 'movie.douban.com',
    'Referer': 'https://movie.douban.com/top250?start=225&filter=',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.104 Safari/537.36',
}


def send_request(url):
    i = 0
    while i <= 3:
        try:
            print("[INFO]请求url" + url)
            return requests.get(url=url, headers=headers).content
        except Exception as e:
            print("[INFO] %s%s" % (e, url))
            i += 1

def parse_page(url):
    response = send_request(url)
    html = etree.HTML(response)
    node_list = html.xpath("//div[@class='info']")
    for moive in node_list:
        title = moive.xpath('.//a/span/text()')[0]
        score = moive.xpath('.//div[@class="bd"]//span[@class="rating_num"]/text()')[0]

        # 将每一部电影的名称跟评分加入到队列
        print(score + "\t" + title)


def main():
    base_url = 'https://movie.douban.com/top250?start='
    url_list = [base_url + str(num) for num in range(0, 225 + 1, 25)]
    for url in url_list:
        parse_page(url)






if __name__ == "__main__":
    start = time.time()
    main()
    print("[INFO]耗时：%s" % (time.time() - start))


```

![1551178798584](C:\Users\带鱼\AppData\Roaming\Typora\typora-user-images\1551178798584.png)

